# 关于实用工具类 common-bin 的 command.js 学习
[common-bin](https://github.com/node-modules/common-bin) 依赖是封装了yargs操作的bin工具类库，支持 async / generator 操作。在需要实现更多功能的自定义xxx-bin时，可通过使用this.yargs来自定义yargs配置。PS：说人话就是一个命令行依赖包。

## 储备知识
本篇学习记录是对于command.js这个文件的学习，核心是对CommonBin类中指令方法的定义。以下介绍都是在command.js中引用到的Node.js部分模块，本文只做粗略的介绍，如需深入学习可通过链接或者Google自行解决。[Node.js中文网->](http://nodejs.cn/api/fs.html)
### 1、Yargs
Yargs是一个用于创建交互式命令行工具的npm包，会这个包来解析命令行参数。[源码传送门->](https://github.com/yargs/yargs)

### 2、Co函数库
用于 Generator 函数的自动执行，本质上co 函数库其实就是将两种自动执行器（Thunk 函数和 Promise 对象），包装成一个库。[源码传送门->](https://github.com/tj/co/blob/master/index.js)

PS：Generator 函数是一个异步操作的容器，它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。

### 3、assert模块
assert模块是Node的内置模块，主要用于断言。如果表达式不符合预期，就抛出一个错误。
[阮一峰老师关于assert模块使用说明->](https://javascript.ruanyifeng.com/nodejs/assert.html)

### 4、fs模块
提供了用于与文件系统进行交互（以类似于标准 POSIX 函数的方式）的 API，所有的文件系统操作都具有同步和异步的形式。

异步的形式总是把完成回调作为其最后一个参数。 传给完成回调的参数取决于具体方法，但第一个参数总是预留给异常。 如果操作被成功地完成，则第一个参数会为 null 或 undefined。且当使用异步的方法时，无法保证顺序。

当使用同步的操作时，发生的异常会被立即地抛出，可以使用 try…catch 处理，也可以冒泡。[使用方法->](http://nodejs.cn/api/fs.html)

### 5、path模块
提供了一些用于处理文件与目录的路径的实用工具。[使用方法->](http://nodejs.cn/api/path.html)

### 6、semver模块
一个语义化版本号管理的模块，可以实现版本号的解析和比较，规范版本号的格式。[官网->](https://semver.org/)

### 7、change-case模块
修改字符串为驼峰模式。

### 8、chalk模块
修改控制台中字符串的样式。 [源码传送门->](https://github.com/chalk/chalk)

## command.js源码详解
```bash

  run() {
    this.showHelp();
  }

  /**
   * 打印help信息到控制台
   * @param {String} [level=log] - console level
   */
  showHelp(level = 'log') {
    this.yargs.showHelp(level);
  }
```
## load()方法用来加载子命令

通过判断fs.existsSync检测输入的文件路径是否存在并判断fullPath是否描述文件系统目录，以输出提示信息。通过fs.readdirSync同步获取路径中子文件的文件名，将js文件的文件名加入names数组并通过add方法加入子命令集中。
```bash

  /**
   * 加载子命令
   * @param {String} fullPath - the command directory
   * @example `load(path.join(__dirname, 'command'))`
   */
  load(fullPath) {
    assert(fs.existsSync(fullPath) && fs.statSync(fullPath).isDirectory(),
      `${fullPath} should exist and be a directory`);

    // load entire directory
    const files = fs.readdirSync(fullPath);
    const names = [];
    for (const file of files) {
      if (path.extname(file) === '.js') {
        const name = path.basename(file).replace(/\.js$/, '');
        names.push(name);
        this.add(name, path.join(fullPath, file));
      }
    }

    debug('[%s] loaded command `%s` from directory `%s`',
      this.constructor.name, names, fullPath);
  }

```
## add（）方法用来添加子命令

instanceof是用来判断一个对象是否存在与另一个对象的原型链上。  
在add中会排除引入非CommonBin子类的命令类，同时在控制台上输出目标路径为非文件时的提示。
```bash
  /**
   * 添加子命令
   * @param {String} name - a command name
   * @param {String|Class} target - special file path (must contains ext) or Command Class
   * @example `add('test', path.join(__dirname, 'test_command.js'))`
   */
  add(name, target) {
    assert(name, `${name} is required`);
    if (!(target.prototype instanceof CommonBin)) {
      assert(fs.existsSync(target) && fs.statSync(target).isFile(), `${target} is not a file.`);
      debug('[%s] add command `%s` from `%s`', this.constructor.name, name, target);
      target = require(target);
      // 尝试要求引入的文件包含es模块
      if (target && target.__esModule && target.default) {
        target = target.default;
      }
      assert(target.prototype instanceof CommonBin,
        'command class should be sub class of common-bin');
    }
    this[COMMANDS].set(name, target);
  }
```
## alias（）给已存在的命令添加别名
在控制台输出对别名是否输入的判断、是否有重复的命名的判断。
```bash

  /**
   * 给已存在的命令添加别名
   * @param {String} alias - alias command
   * @param {String} name - exist command
   */
  alias(alias, name) {
    assert(alias, 'alias command name is required');
    assert(this[COMMANDS].has(name), `${name} should be added first`);
    debug('[%s] set `%s` as alias of `%s`', this.constructor.name, alias, name);
    this[COMMANDS].set(alias, this[COMMANDS].get(name));
  }

```
## start()方法用来解析命令行参数并调用[DISPATCH]方法来执行参数中的子命令
```bash
  /**
   * 开始bin的重要部分的进程
   */
  start() {
    co(function* () {
      // 用 `--get-yargs-completions` 去替换我们的KEY, 因此yargs不会阻断我们的调度[DISPATCH]
      const index = this.rawArgv.indexOf('--get-yargs-completions');
      if (index !== -1) {
        // bash 将会要求 `--get-yargs-completions my-git remote add`, 所以我们得移除2
        this.rawArgv.splice(index, 2, `--AUTO_COMPLETIONS=${this.rawArgv.join(',')}`);
      }
      yield this[DISPATCH]();
    }.bind(this)).catch(this.errorHandler.bind(this));
  }
```
### * DISPATCH方法用来执行子命令（通过调用子命令的run方法）
当this[COMMANDS]的子命令存在时会通过getSubCommandInstance（）方法获取子命令集，再执行子命令的* DISPATCH方法。

当子命令不存在时，才会用 yield this.helper.callFn(this.run, [ context ], this);来执行真正的run方法（此处的run执行的应该是子类里的run，而非common-bin.js的run函数）。
```bash

  /**
   * dispatch command, either `subCommand.exec` or `this.run`
   * @param {Object} context - context object
   * @param {String} context.cwd - process.cwd()
   * @param {Object} context.argv - argv parse result by yargs, `{ _: [ 'start' ], '$0': '/usr/local/bin/common-bin', baseDir: 'simple'}`
   * @param {Array} context.rawArgv - the raw argv, `[ "--baseDir=simple" ]`
   * @private
   */
  * [DISPATCH]() {
    // define --help and --version by default
    this.yargs
      // .reset()
      .completion()
      .help()
      .version()
      .wrap(120)
      .alias('h', 'help')
      .alias('v', 'version')
      .group([ 'help', 'version' ], 'Global Options:');

    // get parsed argument without handling helper and version
    const parsed = yield this[PARSE](this.rawArgv);
    const commandName = parsed._[0];

    if (parsed.version && this.version) {
      console.log(this.version);
      return;
    }

    // 如果子命令存在
    if (this[COMMANDS].has(commandName)) {
      const Command = this[COMMANDS].get(commandName);
      const rawArgv = this.rawArgv.slice();
      rawArgv.splice(rawArgv.indexOf(commandName), 1);

      debug('[%s] dispatch to subcommand `%s` -> `%s` with %j', this.constructor.name, commandName, Command.name, rawArgv);
      const command = this.getSubCommandInstance(Command, rawArgv);
      yield command[DISPATCH]();
      return;
    }

    // 注册打印命令
    for (const [ name, Command ] of this[COMMANDS].entries()) {
      this.yargs.command(name, Command.prototype.description || '');
    }

    debug('[%s] exec run command', this.constructor.name);
    const context = this.context;

    // bash的打印完成
    if (context.argv.AUTO_COMPLETIONS) {
      // 切开为了删除附加的 `--AUTO_COMPLETIONS=` 
      this.yargs.getCompletion(this.rawArgv.slice(1), completions => {
        // console.log('%s', completions)
        completions.forEach(x => console.log(x));
      });
    } else {
      // handle by self
      yield this.helper.callFn(this.run, [ context ], this);
    }
  }
```
## get（）获得操作的具体内容
helper.unparseArgv(argv)：解析argv并将其更改为数组样式  
helper.extractExecArgv(argv)：从argv中提取execArgv
```bash
  /**
   * 内容获取器, 默认操作为删除 `help` / `h` / `version`
   * @return {Object} context - { cwd, env, argv, rawArgv }
   * @protected
   */
  get context() {
    const argv = this.yargs.argv;
    const context = {
      argv,
      cwd: process.cwd(),
      env: Object.assign({}, process.env),
      rawArgv: this.rawArgv,
    };

    argv.help = undefined;
    argv.h = undefined;
    argv.version = undefined;
    argv.v = undefined;

    // 删除别名结果
    if (this.parserOptions.removeAlias) {
      const aliases = this.yargs.getOptions().alias;
      for (const key of Object.keys(aliases)) {
        aliases[key].forEach(item => {
          argv[item] = undefined;
        });
      }
    }

    // 删除驼峰案例结果
    if (this.parserOptions.removeCamelCase) {
      for (const key of Object.keys(argv)) {
        if (key.includes('-')) {
          argv[changeCase.camel(key)] = undefined;
        }
      }
    }

    // 提取 execArgv
    if (this.parserOptions.execArgv) {
      // extract from command argv
      let { debugPort, debugOptions, execArgvObj } = this.helper.extractExecArgv(argv);

      // 从 WebStorm env `$NODE_DEBUG_OPTION` 提取
      // 提示: WebStorm 2019 将不再暴露 env, 取而代之, 用 `env.NODE_OPTIONS="--require="`, 但是我们不能提取它.
      if (context.env.NODE_DEBUG_OPTION) {
        console.log('Use $NODE_DEBUG_OPTION: %s', context.env.NODE_DEBUG_OPTION);
        const argvFromEnv = parser(context.env.NODE_DEBUG_OPTION);
        const obj = this.helper.extractExecArgv(argvFromEnv);
        debugPort = obj.debugPort || debugPort;
        Object.assign(debugOptions, obj.debugOptions);
        Object.assign(execArgvObj, obj.execArgvObj);
      }

      // `--expose_debug_as` 7.x+ 版本不再支持 
      if (execArgvObj.expose_debug_as && semver.gte(process.version, '7.0.0')) {
        console.warn(chalk.yellow(`Node.js runtime is ${process.version}, and inspector protocol is not support --expose_debug_as`));
      }

      // 从原始的 argv 中删除
      for (const key of Object.keys(execArgvObj)) {
        argv[key] = undefined;
        argv[changeCase.camel(key)] = undefined;
      }

      // 暴露 execArgv
      const self = this;
      context.execArgvObj = execArgvObj;

      // 将execArgvObj转换为execArgv
      // `--require` should be `--require abc --require 123`, not allow `=`
      // `--debug` should be `--debug=9999`, only allow `=`
      Object.defineProperty(context, 'execArgv', {
        get() {
          const lazyExecArgvObj = context.execArgvObj;
          const execArgv = self.helper.unparseArgv(lazyExecArgvObj, { excludes: [ 'require' ] });
          // 将require转换为execArgv
          let requireArgv = lazyExecArgvObj.require;
          if (requireArgv) {
            if (!Array.isArray(requireArgv)) requireArgv = [ requireArgv ];
            requireArgv.forEach(item => {
              execArgv.push('--require');
              execArgv.push(item.startsWith('./') || item.startsWith('.\\') ? path.resolve(context.cwd, item) : item);
            });
          }
          return execArgv;
        },
      });

      // 当匹配时才导出 debugPort
      if (Object.keys(debugOptions).length) {
        context.debugPort = debugPort;
        context.debugOptions = debugOptions;
      }
    }

    return context;
  }
```

##  common-bin 使用须知
### 方法
- start() - 启动程序，且只能在bin文件中使用一次。
- run(context)
  * 当没有找到子命令时，应该执行这个以提供命令操作.
  * 支持返回 promise 的方法 如 generator / async 方法 / 普通方法 .
  * context is { cwd, env, argv, rawArgv }
    - cwd - process.cwd()
    - env - 从 process.env 克隆 env 对象
    - argv - yargs解析argv的结果, { _: [ 'start' ], '$0': '/usr/local/bin/common-bin', baseDir: 'simple'}
    - rawArgv - 原始的argv, [ "--baseDir=simple" ]
- load(fullPath) - 注册整个目录为命令.
- add(name, target) - 用命令名注册特殊的命令，目标命令可以是完整的路径或者类.
- alias(alias, name) - 用现有/已存在的命令注册.
- showHelp() - 打印用法信息到控制台.
- options= - 一个设置 yargs.options 的快捷方法.
- usage= - 一个设置 yargs.usage 的快捷方法.

### 依赖
- description - {String} getter, 只在帮助控制台输出中的子命令时显示此描述
- helper - {Object} helper 实例
- yargs - {Object} yargs实例，用于高级自定义用法
- options - {Object} a setter, 设置yargs的选项
- version - {String} 定制版本, 为了支持懒加载可以被定义为 getter.
- parserOptions - {Object} 控制 context 解析规则.
  - execArgv - {Boolean} 是否将execArgv提取到context.execArgv.
  - removeAlias - {Boolean} 是否从argv中删除别名密钥.
  - removeCamelCase - {Boolean} 是否从argv中删除驼峰案例密钥.



## 支持返回 promise 的方法的原因
这里通过callFn的定义可以看出，函数区分了不同异步调用方法的入口，在用yield继续执行之前没有执行完的函数。（感觉这里区分函数类型的操作并没有实际执行意义）
```bash
exports.callFn = function* (fn, args = [], thisArg) {
  if (!is.function(fn)) return;
  if (is.generatorFunction(fn)) {
    return yield fn.apply(thisArg, args);
  }
  const r = fn.apply(thisArg, args);
  if (is.promise(r)) {
    return yield r;
  }
  return r;
};
```
