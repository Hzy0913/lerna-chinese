<p align="center">
  <img alt="Lerna" src="https://cloud.githubusercontent.com/assets/952783/15271604/6da94f96-1a06-11e6-8b04-dc3171f79a90.png" width="480">
</p>

<p align="center">
   一个管理使用多个包的JavaScript项目工具
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/lerna"><img alt="NPM Status" src="https://img.shields.io/npm/v/lerna.svg?style=flat"></a>
  <a href="https://travis-ci.org/lerna/lerna"><img alt="Travis Status" src="https://img.shields.io/travis/lerna/lerna/master.svg?style=flat&label=travis"></a>
</p>

- [About](#about)
- [Getting Started](#getting-started)
- [How It Works](#how-it-works)
- [Troubleshooting](#troubleshooting)
- Commands
  - [`lerna publish`](./commands/publish#readme)
  - [`lerna version`](./commands/version#readme)
  - [`lerna bootstrap`](./commands/bootstrap#readme)
  - [`lerna list`](./commands/list#readme)
  - [`lerna changed`](./commands/changed#readme)
  - [`lerna diff`](./commands/diff#readme)
  - [`lerna exec`](./commands/exec#readme)
  - [`lerna run`](./commands/run#readme)
  - [`lerna init`](./commands/init#readme)
  - [`lerna add`](./commands/add#readme)
  - [`lerna clean`](./commands/clean#readme)
  - [`lerna import`](./commands/import#readme)
  - [`lerna link`](./commands/link#readme)
  - [`lerna create`](./commands/create#readme)
- [Concepts](#concepts)
- [Lerna.json](#lernajson)
- [Global Flags](./core/global-options)
- [Filter Flags](./core/filter-options)

## 关于

拆分大型代码库为单独的独立版本包对于代码共享非常有用。然而，跨多个仓库进行修改变更是混乱且难以跟踪的，跨仓库的调试也变得非常复杂。 

为了解决这些 (和其他一些) 问题，一些项目将他们的代码库组织为multi-package仓库形式 (也称为 [monorepos](https://github.com/babel/babel/blob/master/doc/design/monorepo.md))。这些项目有[Babel](https://github.com/babel/babel/tree/master/packages), [React](https://github.com/facebook/react/tree/master/packages), [Angular](https://github.com/angular/angular/tree/master/modules),
[Ember](https://github.com/emberjs/ember.js/tree/master/packages), [Meteor](https://github.com/meteor/meteor/tree/devel/packages), [Jest](https://github.com/facebook/jest/tree/master/packages), 还有许多其他人开发他们所有的包在单个的仓库中。

**Lerna是一个可以使用git和npm来优化管理多个包仓库工作流的工具。**

Lerna还可以减少开发和构建环境中处理许多副本包的时间和空间—通常将一个项目分成许多不同的NPM包是有缺点的。详见[hoist documentation](doc/hoist.md)。

### 一个 Lerna 的仓库看起来是什么样的?

其实没什么特殊的。你的文件结构可能就像下面这样:

```
my-lerna-repo/
  package.json
  packages/
    package-1/
      package.json
    package-2/
      package.json
```

### Lerna 能做什么呢?

Lerna中的两个主要命令是`lerna bootstrap`和`lerna publish`。

`bootstrap` 会将仓库中的依赖链接在一起。`publish` 用来帮助发布以及更新包。

### 什么是 Lerna 不能做的呢?

Lerna不是无服务器monorepos的部署工具。Hoisting可能与传统的无服务器单仓库(monorepo)部署技术不兼容。

## 起步

> 下面的说明是针对Lerna 3.x的。
> 我们建议在新的Lerna项目中使用它而不是2.x版本。

让我们从使用[npm](https://www.npmjs.com/)将Lerna安装为项目的开发依赖项开始。 

```sh
$ mkdir lerna-repo && cd $_
$ npx lerna init
```
这将会创建一个`lerna.json`的配置文件和一个`packages`文件夹，所以你的文件夹现在会是下面这样：

```
lerna-repo/
  packages/
  package.json
  lerna.json
```

## 它如何工作

Lerna 允许你使用两种模式之一来管理项目：固定模式或独立模式。 

### 固定/锁定 模式 (默认)

固定模式的 Lerna 项目在单个版本线上运行。版本会保存在项目根目录下的`lerna.json`文件中的`version`中。当你执行`lerna publish`时, 如果模块自上次发布后有过更新过, 它将为你更新要发布的新版本号. 这意味着你只在需要时才发布包的新版本。

这是[Babel](https://github.com/babel/babel)目前正在使用的模式. 如果你要自动的将所有包版本捆绑在一起，可使用该模式。但这种存在一个问题是，任何包中的主版本更改都将导致所有包生成一个新的主版本号。 

### Independent mode

`lerna init --independent`

Independent mode Lerna projects allows maintainers to increment package versions independently of each other. Each time you publish, you will get a prompt for each package that has changed to specify if it's a patch, minor, major or custom change.

Independent mode allows you to more specifically update versions for each package and makes sense for a group of components. Combining this mode with something like [semantic-release](https://github.com/semantic-release/semantic-release) would make it less painful. (There is work on this already at [atlassian/lerna-semantic-release](https://github.com/atlassian/lerna-semantic-release)).

> Set the `version` key in `lerna.json` to `independent` to run in independent mode.

## Troubleshooting

If you encounter any issues while using Lerna please check out our [Troubleshooting](doc/troubleshooting.md)
document where you might find the answer to your problem.

## Frequently asked questions

See [FAQ.md](FAQ.md).

## Concepts

Lerna will log to a `lerna-debug.log` file (same as `npm-debug.log`) when it encounters an error running a command.

Lerna also has support for [scoped packages](https://docs.npmjs.com/misc/scope).

Run `lerna --help` to see all available commands and options.

### lerna.json

```json
{
  "version": "1.1.3",
  "npmClient": "npm",
  "command": {
    "publish": {
      "ignoreChanges": ["ignored-file", "*.md"],
      "message": "chore(release): publish",
      "registry": "https://npm.pkg.github.com"
    },
    "bootstrap": {
      "ignore": "component-*",
      "npmClientArgs": ["--no-package-lock"]
    }
  },
  "packages": ["packages/*"]
}
```

- `version`: the current version of the repository.
- `npmClient`: an option to specify a specific client to run commands with (this can also be specified on a per command basis). Change to `"yarn"` to run all commands with yarn. Defaults to "npm".
- `command.publish.ignoreChanges`: an array of globs that won't be included in `lerna changed/publish`. Use this to prevent publishing a new version unnecessarily for changes, such as fixing a `README.md` typo.
- `command.publish.message`: a custom commit message when performing version updates for publication. See [@lerna/version](commands/version#--message-msg) for more details.
- `command.publish.registry`: use it to set a custom registry url to publish to instead of
npmjs.org, you must already be authenticated if required.
- `command.bootstrap.ignore`: an array of globs that won't be bootstrapped when running the `lerna bootstrap` command.
- `command.bootstrap.npmClientArgs`: array of strings that will be passed as arguments directly to `npm install` during the `lerna bootstrap` command.
- `command.bootstrap.scope`: an array of globs that restricts which packages will be bootstrapped when running the `lerna bootstrap` command.
- `packages`: Array of globs to use as package locations.

The packages config in `lerna.json` is a list of globs that match directories containing a `package.json`, which is how lerna recognizes "leaf" packages (vs the "root" `package.json`, which is intended to manage the dev dependencies and scripts for the entire repo).

By default, lerna initializes the packages list as `["packages/*"]`, but you can also use another directory such as `["modules/*"]`, or `["package1", "package2"]`. The globs defined are relative to the directory that `lerna.json` lives in, which is usually the repository root. The only restriction is that you can't directly nest package locations, but this is a restriction shared by "normal" npm packages as well.

For example, `["packages/*", "src/**"]` matches this tree:

```
packages/
├── foo-pkg
│   └── package.json
├── bar-pkg
│   └── package.json
├── baz-pkg
│   └── package.json
└── qux-pkg
    └── package.json
src/
├── admin
│   ├── my-app
│   │   └── package.json
│   ├── stuff
│   │   └── package.json
│   └── things
│       └── package.json
├── profile
│   └── more-things
│       └── package.json
├── property
│   ├── more-stuff
│   │   └── package.json
│   └── other-things
│       └── package.json
└── upload
    └── other-stuff
        └── package.json
```

Locating leaf packages under `packages/*` is considered a "best-practice", but is not a requirement for using Lerna.

#### Legacy Fields

Some `lerna.json` fields are no longer in use. Those of note include:
* `lerna`: originally used to indicate the current version of Lerna. [Made obsolete](https://github.com/lerna/lerna/pull/1122) and [removed](https://github.com/lerna/lerna/pull/1225) in v3

### Common `devDependencies`

Most `devDependencies` can be pulled up to the root of a Lerna repo with `lerna link convert`

The above command will automatically hoist things and use relative `file:` specifiers.

Hoisting has a few benefits:
- All packages use the same version of a given dependency
- Can keep dependencies at the root up-to-date with an automated tool such as [GreenKeeper](https://greenkeeper.io/)
- Dependency installation time is reduced
- Less storage is needed

Note that `devDependencies` providing "binary" executables that are used by
npm scripts still need to be installed directly in each package where they're
used.

For example the `nsp` dependency is necessary in this case for `lerna run nsp`
(and `npm run nsp` within the package's directory) to work correctly:

```json
{
  "scripts": {
    "nsp": "nsp"
  },
  "devDependencies": {
    "nsp": "^2.3.3"
  }
}
```

### Git Hosted Dependencies

Lerna allows target versions of local dependent packages to be written as a [git remote url](https://docs.npmjs.com/cli/install) with a `committish` (e.g., `#v1.0.0` or `#semver:^1.0.0`) instead of the normal numeric version range.
This allows packages to be distributed via git repositories when packages must be private and a [private npm registry is not desired](https://www.dotconferences.com/2016/05/fabien-potencier-monolithic-repositories-vs-many-repositories).

Please note that lerna does _not_ perform the actual splitting of git history into the separate read-only repositories. This is the responsibility of the user. (See [this comment](https://github.com/lerna/lerna/pull/1033#issuecomment-335894690) for implementation details)

```
// packages/pkg-1/package.json
{
  name: "pkg-1",
  version: "1.0.0",
  dependencies: {
    "pkg-2": "github:example-user/pkg-2#v1.0.0"
  }
}

// packages/pkg-2/package.json
{
  name: "pkg-2",
  version: "1.0.0"
}
```

In the example above,

- `lerna bootstrap` will properly symlink `pkg-2` into `pkg-1`.
- `lerna publish` will update the committish (`#v1.0.0`) in `pkg-1` when `pkg-2` changes.

### README Badge

Using Lerna? Add a README badge to show it off: [![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/)

```
[![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/)
```

### Wizard

If you prefer some guidance for cli (in case you're about to start using lerna or introducing it to a new team), you might like [lerna-wizard](https://github.com/szarouski/lerna-wizard). It will lead you through a series of well-defined steps:

![lerna-wizard demo image](https://raw.githubusercontent.com/szarouski/lerna-wizard/2e269fb5a3af7100397a1f874cea3fa78089486e/demo.png)
