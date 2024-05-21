# eslint-plugin-react

[![github actions][actions-image]][actions-url]
[![Maintenance Status][status-image]][status-url]
[![NPM version][npm-image]][npm-url]
[![Tidelift][tidelift-image]][tidelift-url]

针对`eslint`的React特定的lint规则

## 安装

```sh
npm install eslint eslint-plugin-react --save-dev
```

也可以全局安装ESLint（使用`npm install -g eslint`），但不推荐这样做，你使用的任何插件或可共享配置都必须在任何情况下本地安装。

## 配置（旧版：`.eslintrc*`） <a id="configuration"></a>

使用[我们的预设](#recommended)获取合理的默认值：

```json
    "extends": [
        "eslint:recommended",
        "plugin:react/recommended"
    ]
```

如果你正在使用[React 17的新JSX转换](https://reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html#removing-unused-react-imports)，在你的eslint配置中扩展[`react/jsx-runtime`](https://github.com/jsx-eslint/eslint-plugin-react/blob/c8917b0885094b5e4cc2a6f613f7fb6f16fe932e/index.js#L163-L176)（在`"extends"`中添加`"plugin:react/jsx-runtime"`）以禁用相关规则。

你还应指定将在所有插件规则中共享的设置。([更多关于eslint共享设置的信息](https://eslint.org/docs/user-guide/configuring/configuration-files#adding-shared-settings))

```json5
{
    "settings": {
        "react": {
            "createClass": "createReactClass", // 用于使用的组件工厂的正则表达式，
                                               // 默认为 "createReactClass"
            "pragma": "React",  // 使用的Pragma，默认为 "React"
            "fragment": "Fragment",  // 使用的Fragment（可能是<pragma>的属性），默认为 "Fragment"
            "version": "detect", // React版本。"detect"会自动选择你已安装的版本。
                                 // 你也可以使用`16.0`、`16.3`等，如果你想覆盖检测到的值。
                                 // 如果缺失，它将默认为"latest"并发出警告，在未来将默认为"detect"
            "flowVersion": "0.53" // Flow版本
        },
        "propWrapperFunctions": [
                // 用于包装propTypes的任何函数的名称，例如`forbidExtraProps`。如果没有设置，任何包装在函数中的propTypes都将被跳过。
                "forbidExtraProps",
                {"property": "freeze", "object": "Object"},
                {"property": "myFavoriteWrapper"},
                // 用于检查确切的prop包装器的规则
                {"property": "forbidExtraProps", "exact": true}
        ],
        "componentWrapperFunctions": [
                // 用于包装组件的任何函数的名称，例如Mobx的`observer`函数。如果没有设置，这些函数包装的组件将被跳过。
                "observer", // `property`
                {"property": "styled"}, // `object`是可选的
                {"property": "observer", "object": "Mobx"},
                {"property": "observer", "object": "<pragma>"} // 将`object`设置为`settings.react.pragma`的值
        ],
        "formComponents": [
            // 用作<form>的替代品的组件，例如<Form endpoint={ url } />
            "CustomForm",
            {"name": "SimpleForm", "formAttribute": "endpoint"},
            {"name": "Form", "formAttribute": ["registerEndpoint", "loginEndpoint"]}, // 如果需要，允许指定多个属性
        ],
        "linkComponents": [
            // 用作<a>的替代品的组件，例如<Link to={ url } />
            "Hyperlink",
            {"name": "MyLink", "linkAttribute": "to"},
            {"name": "Link", "linkAttribute": ["to", "href"]}, // 如果需要，允许指定多个属性
        ]
    }
}
```

如果你不使用预设，你将需要指定单独的规则并添加额外的配置。

在插件部分添加"react"。

```json
{
    "plugins": [
        "react"
    ]
}
```

启用JSX支持。

对于`eslint` 2+

```json
{
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        }
    }
}
```

启用你想使用的规则。

```json
    "rules": {
        "react/jsx-uses-react": "error",
        "react/jsx-uses-vars": "error",
    }
```

### 可共享的配置

#### 推荐的

此插件导出一个`recommended`配置，该配置强制执行React的良好实践。

要启用此配置，请在你的`.eslintrc`配置文件中使用`extends`属性：

```json
{
    "extends": ["eslint:recommended", "plugin:react/recommended"]
}
```

查看[`eslint`文档](https://eslint.org/docs/user-guide/configuring/configuration-files#extending-configuration-files)以获取有关扩展配置文件的更多信息。

#### 全部

此插件还导出一个`all`配置，该配置包含每个可用的规则。
这与`eslint:all`规则配合得很好。

```json
{
    "plugins": [
        "react"
    ],
    "extends": ["eslint:all", "plugin:react/all"]
}
```

**注意**：这些配置将导入`eslint-plugin-react`并在[解析器选项](https://eslint.org/docs/user-guide/configuring/language-options#specifying-parser-options)中启用JSX。

## 配置（新版：`eslint.config.js`）

从[`v8.21.0`](https://github.com/eslint/eslint/releases/tag/v8.21.0)开始，eslint宣布了一个新的配置系统。
在新系统中，不再使用`.eslintrc*`。`eslint.config.js`将成为默认的配置文件名。
在eslint `v8`中，旧系统（`.eslintrc*`）仍将得到支持，而在eslint `v9`中，只有新系统将得到支持。

从[`v8.23.0`](https://github.com/eslint/eslint/releases/tag/v8.23.0)开始，eslint CLI开始查找`eslint.config.js`。
**所以，如果你的eslint是`>=8.23.0`，你就完全准备好使用新的配置系统了。**

你可能想查看官方的博客文章，

- <https://eslint.org/blog/2022/08/new-config-system-part-1/>
- <https://eslint.org/blog/2022/08/new-config-system-part-2/>
- <https://eslint.org/blog/2022/08/new-config-system-part-3/>

以及[官方文档](https://eslint.org/docs/latest/user-guide/configuring/configuration-files-new)。

### 插件

`eslint-plugin-react`的默认导出是一个插件对象。

```js
const react = require('eslint-plugin-react');
const globals = require('globals');

module.exports = [
  …
  {
    files: ['**/*.{js,jsx,mjs,cjs,ts,tsx}'],
    plugins: {
      react,
    },
    languageOptions: {
      parserOptions: {
        ecmaFeatures: {
          jsx: true,
        },
      },
      globals: {
        ...globals.browser,
      },
    },
    rules: {
      // ... 你想要的任何规则
      'react/jsx-uses-react': 'error',
      'react/jsx-uses-vars': 'error',
     },
    // ... 其他的省略了以简洁为主
  },
  …
];
```

### 配置共享设置

参考[官方文档](https://eslint.org/docs/latest/user-guide/configuring/configuration-files-new#configuring-shared-settings)。

`settings.react`对象的模式将与上述旧配置部分中已经描述的完全相同。

### 可共享的配置

还有3个可共享的配置。

- `eslint-plugin-react/configs/all`
- `eslint-plugin-react/configs/recommended`
- `eslint-plugin-react/configs/jsx-runtime`

如果你的eslint.config.js是ESM，包括`.js`扩展名（例如`eslint-plugin-react/recommended.js`）。注意，下一个semver-major将要求省略这些导入的扩展名。

**注意**：这些配置将导入`eslint-plugin-react`并在[`languageOptions.parserOptions`](https://eslint.org/docs/latest/user-guide/configuring/configuration-files-new#configuration-objects)中启用JSX。

在新的配置系统中，`plugin:`协议（例如`plugin:react/recommended`）不再有效。
由于eslint不会自动导入预设配置（可共享配置），你需要明确地自己做。

```js
const reactRecommended = require('eslint-plugin-react/configs/recommended');

module.exports = [
  …
  reactRecommended, // 这不是一个插件对象，而是一个可共享的配置对象
  …
];
```

你当然可以添加/覆盖一些属性。

**注意**：我们的可共享配置不预先配置`files`或[`languageOptions.globals`](https://eslint.org/docs/latest/user-guide/configuring/configuration-files-new#configuration-objects)。
在大多数情况下，你可能想要自己配置一些属性。

```js
const reactRecommended = require('eslint-plugin-react/configs/recommended');
const globals = require('globals');

module.exports = [
  …
  {
    files: ['**/*.{js,mjs,cjs,jsx,mjsx,ts,tsx,mtsx}'],
    ...reactRecommended,
    languageOptions: {
      ...reactRecommended.languageOptions,
      globals: {
        ...globals.serviceworker,
        ...globals.browser,
      },
    },
  },
  …
];
```

上面的例子与下面的例子相同，因为新的配置系统基于链式。

```js
const reactRecommended = require('eslint-plugin-react/configs/recommended');
const globals = require('globals');

module.exports = [
  …
  {
    files: ['**/*.{js,mjs,cjs,jsx,mjsx,ts,tsx,mtsx}'],
    ...reactRecommended,
  },
  {
    files: ['**/*.{js,mjs,cjs,jsx,mjsx,ts,tsx,mtsx}'],
    languageOptions: {
      globals: {
        ...globals.serviceworker,
        ...globals.browser,
      },
    },
  },
  …
];
```

## 支持的规则列表

<!-- begin auto-generated rules list -->

💼 在[配置](https://github.com/jsx-eslint/eslint-plugin-react/#shareable-configs)中启用。\
🚫 在[配置](https://github.com/jsx-eslint/eslint-plugin-react/#shareable-configs)中禁用。\
🏃 在`jsx-runtime`[配置](https://github.com/jsx-eslint/eslint-plugin-react/#shareable-configs)中设置。\
☑️ 在`recommended`[配置](https://github.com/jsx-eslint/eslint-plugin-react/#shareable-configs)中设置。\
🔧 可以通过[`--fix` CLI选项](https://eslint.org/docs/user-guide/command-line-interface#--fix)自动修复。\
💡 可以通过[编辑器建议](https://eslint.org/docs/latest/use/core-concepts#rule-suggestions)手动修复。\
❌ 已弃用。

| Name                                                                                         | Description                                                                                                                                  | 💼 | 🚫 | 🔧 | 💡 | ❌  |
| :------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------- | :- | :- | :- | :- | :- |
| [boolean-prop-naming](docs/rules/boolean-prop-naming.md)                                     | Enforces consistent naming for boolean props                                                                                                 |    |    |    |    |    |
| [button-has-type](docs/rules/button-has-type.md)                                             | Disallow usage of `button` elements without an explicit `type` attribute                                                                     |    |    |    |    |    |
| [checked-requires-onchange-or-readonly](docs/rules/checked-requires-onchange-or-readonly.md) | Enforce using `onChange` or `readonly` attribute when `checked` is used                                                                      |    |    |    |    |    |
| [default-props-match-prop-types](docs/rules/default-props-match-prop-types.md)               | Enforce all defaultProps have a corresponding non-required PropType                                                                          |    |    |    |    |    |
| [destructuring-assignment](docs/rules/destructuring-assignment.md)                           | Enforce consistent usage of destructuring assignment of props, state, and context                                                            |    |    | 🔧 |    |    |
| [display-name](docs/rules/display-name.md)                                                   | Disallow missing displayName in a React component definition                                                                                 | ☑️ |    |    |    |    |
| [forbid-component-props](docs/rules/forbid-component-props.md)                               | Disallow certain props on components                                                                                                         |    |    |    |    |    |
| [forbid-dom-props](docs/rules/forbid-dom-props.md)                                           | Disallow certain props on DOM Nodes                                                                                                          |    |    |    |    |    |
| [forbid-elements](docs/rules/forbid-elements.md)                                             | Disallow certain elements                                                                                                                    |    |    |    |    |    |
| [forbid-foreign-prop-types](docs/rules/forbid-foreign-prop-types.md)                         | Disallow using another component's propTypes                                                                                                 |    |    |    |    |    |
| [forbid-prop-types](docs/rules/forbid-prop-types.md)                                         | Disallow certain propTypes                                                                                                                   |    |    |    |    |    |
| [function-component-definition](docs/rules/function-component-definition.md)                 | Enforce a specific function type for function components                                                                                     |    |    | 🔧 |    |    |
| [hook-use-state](docs/rules/hook-use-state.md)                                               | Ensure destructuring and symmetric naming of useState hook value and setter variables                                                        |    |    |    | 💡 |    |
| [iframe-missing-sandbox](docs/rules/iframe-missing-sandbox.md)                               | Enforce sandbox attribute on iframe elements                                                                                                 |    |    |    |    |    |
| [jsx-boolean-value](docs/rules/jsx-boolean-value.md)                                         | Enforce boolean attributes notation in JSX                                                                                                   |    |    | 🔧 |    |    |
| [jsx-child-element-spacing](docs/rules/jsx-child-element-spacing.md)                         | Enforce or disallow spaces inside of curly braces in JSX attributes and expressions                                                          |    |    |    |    |    |
| [jsx-closing-bracket-location](docs/rules/jsx-closing-bracket-location.md)                   | Enforce closing bracket location in JSX                                                                                                      |    |    | 🔧 |    |    |
| [jsx-closing-tag-location](docs/rules/jsx-closing-tag-location.md)                           | Enforce closing tag location for multiline JSX                                                                                               |    |    | 🔧 |    |    |
| [jsx-curly-brace-presence](docs/rules/jsx-curly-brace-presence.md)                           | Disallow unnecessary JSX expressions when literals alone are sufficient or enforce JSX expressions on literals in JSX children or attributes |    |    | 🔧 |    |    |
| [jsx-curly-newline](docs/rules/jsx-curly-newline.md)                                         | Enforce consistent linebreaks in curly braces in JSX attributes and expressions                                                              |    |    | 🔧 |    |    |
| [jsx-curly-spacing](docs/rules/jsx-curly-spacing.md)                                         | Enforce or disallow spaces inside of curly braces in JSX attributes and expressions                                                          |    |    | 🔧 |    |    |
| [jsx-equals-spacing](docs/rules/jsx-equals-spacing.md)                                       | Enforce or disallow spaces around equal signs in JSX attributes                                                                              |    |    | 🔧 |    |    |
| [jsx-filename-extension](docs/rules/jsx-filename-extension.md)                               | Disallow file extensions that may contain JSX                                                                                                |    |    |    |    |    |
| [jsx-first-prop-new-line](docs/rules/jsx-first-prop-new-line.md)                             | Enforce proper position of the first property in JSX                                                                                         |    |    | 🔧 |    |    |
| [jsx-fragments](docs/rules/jsx-fragments.md)                                                 | Enforce shorthand or standard form for React fragments                                                                                       |    |    | 🔧 |    |    |
| [jsx-handler-names](docs/rules/jsx-handler-names.md)                                         | Enforce event handler naming conventions in JSX                                                                                              |    |    |    |    |    |
| [jsx-indent](docs/rules/jsx-indent.md)                                                       | Enforce JSX indentation                                                                                                                      |    |    | 🔧 |    |    |
| [jsx-indent-props](docs/rules/jsx-indent-props.md)                                           | Enforce props indentation in JSX                                                                                                             |    |    | 🔧 |    |    |
| [jsx-key](docs/rules/jsx-key.md)                                                             | Disallow missing `key` props in iterators/collection literals                                                                                | ☑️ |    |    |    |    |
| [jsx-max-depth](docs/rules/jsx-max-depth.md)                                                 | Enforce JSX maximum depth                                                                                                                    |    |    |    |    |    |
| [jsx-max-props-per-line](docs/rules/jsx-max-props-per-line.md)                               | Enforce maximum of props on a single line in JSX                                                                                             |    |    | 🔧 |    |    |
| [jsx-newline](docs/rules/jsx-newline.md)                                                     | Require or prevent a new line after jsx elements and expressions.                                                                            |    |    | 🔧 |    |    |
| [jsx-no-bind](docs/rules/jsx-no-bind.md)                                                     | Disallow `.bind()` or arrow functions in JSX props                                                                                           |    |    |    |    |    |
| [jsx-no-comment-textnodes](docs/rules/jsx-no-comment-textnodes.md)                           | Disallow comments from being inserted as text nodes                                                                                          | ☑️ |    |    |    |    |
| [jsx-no-constructed-context-values](docs/rules/jsx-no-constructed-context-values.md)         | Disallows JSX context provider values from taking values that will cause needless rerenders                                                  |    |    |    |    |    |
| [jsx-no-duplicate-props](docs/rules/jsx-no-duplicate-props.md)                               | Disallow duplicate properties in JSX                                                                                                         | ☑️ |    |    |    |    |
| [jsx-no-leaked-render](docs/rules/jsx-no-leaked-render.md)                                   | Disallow problematic leaked values from being rendered                                                                                       |    |    | 🔧 |    |    |
| [jsx-no-literals](docs/rules/jsx-no-literals.md)                                             | Disallow usage of string literals in JSX                                                                                                     |    |    |    |    |    |
| [jsx-no-script-url](docs/rules/jsx-no-script-url.md)                                         | Disallow usage of `javascript:` URLs                                                                                                         |    |    |    |    |    |
| [jsx-no-target-blank](docs/rules/jsx-no-target-blank.md)                                     | Disallow `target="_blank"` attribute without `rel="noreferrer"`                                                                              | ☑️ |    | 🔧 |    |    |
| [jsx-no-undef](docs/rules/jsx-no-undef.md)                                                   | Disallow undeclared variables in JSX                                                                                                         | ☑️ |    |    |    |    |
| [jsx-no-useless-fragment](docs/rules/jsx-no-useless-fragment.md)                             | Disallow unnecessary fragments                                                                                                               |    |    | 🔧 |    |    |
| [jsx-one-expression-per-line](docs/rules/jsx-one-expression-per-line.md)                     | Require one JSX element per line                                                                                                             |    |    | 🔧 |    |    |
| [jsx-pascal-case](docs/rules/jsx-pascal-case.md)                                             | Enforce PascalCase for user-defined JSX components                                                                                           |    |    |    |    |    |
| [jsx-props-no-multi-spaces](docs/rules/jsx-props-no-multi-spaces.md)                         | Disallow multiple spaces between inline JSX props                                                                                            |    |    | 🔧 |    |    |
| [jsx-props-no-spreading](docs/rules/jsx-props-no-spreading.md)                               | Disallow JSX prop spreading                                                                                                                  |    |    |    |    |    |
| [jsx-sort-default-props](docs/rules/jsx-sort-default-props.md)                               | Enforce defaultProps declarations alphabetical sorting                                                                                       |    |    |    |    | ❌  |
| [jsx-sort-props](docs/rules/jsx-sort-props.md)                                               | Enforce props alphabetical sorting                                                                                                           |    |    | 🔧 |    |    |
| [jsx-space-before-closing](docs/rules/jsx-space-before-closing.md)                           | Enforce spacing before closing bracket in JSX                                                                                                |    |    | 🔧 |    | ❌  |
| [jsx-tag-spacing](docs/rules/jsx-tag-spacing.md)                                             | Enforce whitespace in and around the JSX opening and closing brackets                                                                        |    |    | 🔧 |    |    |
| [jsx-uses-react](docs/rules/jsx-uses-react.md)                                               | Disallow React to be incorrectly marked as unused                                                                                            | ☑️ | 🏃 |    |    |    |
| [jsx-uses-vars](docs/rules/jsx-uses-vars.md)                                                 | Disallow variables used in JSX to be incorrectly marked as unused                                                                            | ☑️ |    |    |    |    |
| [jsx-wrap-multilines](docs/rules/jsx-wrap-multilines.md)                                     | Disallow missing parentheses around multiline JSX                                                                                            |    |    | 🔧 |    |    |
| [no-access-state-in-setstate](docs/rules/no-access-state-in-setstate.md)                     | Disallow when this.state is accessed within setState                                                                                         |    |    |    |    |    |
| [no-adjacent-inline-elements](docs/rules/no-adjacent-inline-elements.md)                     | Disallow adjacent inline elements not separated by whitespace.                                                                               |    |    |    |    |    |
| [no-array-index-key](docs/rules/no-array-index-key.md)                                       | Disallow usage of Array index in keys                                                                                                        |    |    |    |    |    |
| [no-arrow-function-lifecycle](docs/rules/no-arrow-function-lifecycle.md)                     | Lifecycle methods should be methods on the prototype, not class fields                                                                       |    |    | 🔧 |    |    |
| [no-children-prop](docs/rules/no-children-prop.md)                                           | Disallow passing of children as props                                                                                                        | ☑️ |    |    |    |    |
| [no-danger](docs/rules/no-danger.md)                                                         | Disallow usage of dangerous JSX properties                                                                                                   |    |    |    |    |    |
| [no-danger-with-children](docs/rules/no-danger-with-children.md)                             | Disallow when a DOM element is using both children and dangerouslySetInnerHTML                                                               | ☑️ |    |    |    |    |
| [no-deprecated](docs/rules/no-deprecated.md)                                                 | Disallow usage of deprecated methods                                                                                                         | ☑️ |    |    |    |    |
| [no-did-mount-set-state](docs/rules/no-did-mount-set-state.md)                               | Disallow usage of setState in componentDidMount                                                                                              |    |    |    |    |    |
| [no-did-update-set-state](docs/rules/no-did-update-set-state.md)                             | Disallow usage of setState in componentDidUpdate                                                                                             |    |    |    |    |    |
| [no-direct-mutation-state](docs/rules/no-direct-mutation-state.md)                           | Disallow direct mutation of this.state                                                                                                       | ☑️ |    |    |    |    |
| [no-find-dom-node](docs/rules/no-find-dom-node.md)                                           | Disallow usage of findDOMNode                                                                                                                | ☑️ |    |    |    |    |
| [no-invalid-html-attribute](docs/rules/no-invalid-html-attribute.md)                         | Disallow usage of invalid attributes                                                                                                         |    |    |    | 💡 |    |
| [no-is-mounted](docs/rules/no-is-mounted.md)                                                 | Disallow usage of isMounted                                                                                                                  | ☑️ |    |    |    |    |
| [no-multi-comp](docs/rules/no-multi-comp.md)                                                 | Disallow multiple component definition per file                                                                                              |    |    |    |    |    |
| [no-namespace](docs/rules/no-namespace.md)                                                   | Enforce that namespaces are not used in React elements                                                                                       |    |    |    |    |    |
| [no-object-type-as-default-prop](docs/rules/no-object-type-as-default-prop.md)               | Disallow usage of referential-type variables as default param in functional component                                                        |    |    |    |    |    |
| [no-redundant-should-component-update](docs/rules/no-redundant-should-component-update.md)   | Disallow usage of shouldComponentUpdate when extending React.PureComponent                                                                   |    |    |    |    |    |
| [no-render-return-value](docs/rules/no-render-return-value.md)                               | Disallow usage of the return value of ReactDOM.render                                                                                        | ☑️ |    |    |    |    |
| [no-set-state](docs/rules/no-set-state.md)                                                   | Disallow usage of setState                                                                                                                   |    |    |    |    |    |
| [no-string-refs](docs/rules/no-string-refs.md)                                               | Disallow using string references                                                                                                             | ☑️ |    |    |    |    |
| [no-this-in-sfc](docs/rules/no-this-in-sfc.md)                                               | Disallow `this` from being used in stateless functional components                                                                           |    |    |    |    |    |
| [no-typos](docs/rules/no-typos.md)                                                           | Disallow common typos                                                                                                                        |    |    |    |    |    |
| [no-unescaped-entities](docs/rules/no-unescaped-entities.md)                                 | Disallow unescaped HTML entities from appearing in markup                                                                                    | ☑️ |    |    |    |    |
| [no-unknown-property](docs/rules/no-unknown-property.md)                                     | Disallow usage of unknown DOM property                                                                                                       | ☑️ |    | 🔧 |    |    |
| [no-unsafe](docs/rules/no-unsafe.md)                                                         | Disallow usage of unsafe lifecycle methods                                                                                                   |    | ☑️ |    |    |    |
| [no-unstable-nested-components](docs/rules/no-unstable-nested-components.md)                 | Disallow creating unstable components inside components                                                                                      |    |    |    |    |    |
| [no-unused-class-component-methods](docs/rules/no-unused-class-component-methods.md)         | Disallow declaring unused methods of component class                                                                                         |    |    |    |    |    |
| [no-unused-prop-types](docs/rules/no-unused-prop-types.md)                                   | Disallow definitions of unused propTypes                                                                                                     |    |    |    |    |    |
| [no-unused-state](docs/rules/no-unused-state.md)                                             | Disallow definitions of unused state                                                                                                         |    |    |    |    |    |
| [no-will-update-set-state](docs/rules/no-will-update-set-state.md)                           | Disallow usage of setState in componentWillUpdate                                                                                            |    |    |    |    |    |
| [prefer-es6-class](docs/rules/prefer-es6-class.md)                                           | Enforce ES5 or ES6 class for React Components                                                                                                |    |    |    |    |    |
| [prefer-exact-props](docs/rules/prefer-exact-props.md)                                       | Prefer exact proptype definitions                                                                                                            |    |    |    |    |    |
| [prefer-read-only-props](docs/rules/prefer-read-only-props.md)                               | Enforce that props are read-only                                                                                                             |    |    | 🔧 |    |    |
| [prefer-stateless-function](docs/rules/prefer-stateless-function.md)                         | Enforce stateless components to be written as a pure function                                                                                |    |    |    |    |    |
| [prop-types](docs/rules/prop-types.md)                                                       | Disallow missing props validation in a React component definition                                                                            | ☑️ |    |    |    |    |
| [react-in-jsx-scope](docs/rules/react-in-jsx-scope.md)                                       | Disallow missing React when using JSX                                                                                                        | ☑️ | 🏃 |    |    |    |
| [require-default-props](docs/rules/require-default-props.md)                                 | Enforce a defaultProps definition for every prop that is not a required prop                                                                 |    |    |    |    |    |
| [require-optimization](docs/rules/require-optimization.md)                                   | Enforce React components to have a shouldComponentUpdate method                                                                              |    |    |    |    |    |
| [require-render-return](docs/rules/require-render-return.md)                                 | Enforce ES5 or ES6 class for returning value in render function                                                                              | ☑️ |    |    |    |    |
| [self-closing-comp](docs/rules/self-closing-comp.md)                                         | Disallow extra closing tags for components without children                                                                                  |    |    | 🔧 |    |    |
| [sort-comp](docs/rules/sort-comp.md)                                                         | Enforce component methods order                                                                                                              |    |    |    |    |    |
| [sort-default-props](docs/rules/sort-default-props.md)                                       | Enforce defaultProps declarations alphabetical sorting                                                                                       |    |    |    |    |    |
| [sort-prop-types](docs/rules/sort-prop-types.md)                                             | Enforce propTypes declarations alphabetical sorting                                                                                          |    |    | 🔧 |    |    |
| [state-in-constructor](docs/rules/state-in-constructor.md)                                   | Enforce class component state initialization style                                                                                           |    |    |    |    |    |
| [static-property-placement](docs/rules/static-property-placement.md)                         | Enforces where React component static properties should be positioned.                                                                       |    |    |    |    |    |
| [style-prop-object](docs/rules/style-prop-object.md)                                         | Enforce style prop value is an object                                                                                                        |    |    |    |    |    |
| [void-dom-elements-no-children](docs/rules/void-dom-elements-no-children.md)                 | Disallow void DOM elements (e.g. `<img />`, `<br />`) from receiving children                                                                |    |    |    |    |    |

<!-- end auto-generated rules list -->

## 其他有用的插件

- Hooks 规则：[eslint-plugin-react-hooks](https://github.com/facebook/react/tree/master/packages/eslint-plugin-react-hooks)
- JSX 可访问性：[eslint-plugin-jsx-a11y](https://github.com/jsx-eslint/eslint-plugin-jsx-a11y)
- React Native：[eslint-plugin-react-native](https://github.com/Intellicode/eslint-plugin-react-native)

## License

`eslint-plugin-react` is licensed under the [MIT License](https://opensource.org/licenses/mit-license.php).

[npm-url]: https://npmjs.org/package/eslint-plugin-react
[npm-image]: https://img.shields.io/npm/v/eslint-plugin-react.svg

[status-url]: https://github.com/jsx-eslint/eslint-plugin-react/pulse
[status-image]: https://img.shields.io/github/last-commit/jsx-eslint/eslint-plugin-react.svg

[tidelift-url]: https://tidelift.com/subscription/pkg/npm-eslint-plugin-react?utm_source=npm-eslint-plugin-react&utm_medium=referral&utm_campaign=readme
[tidelift-image]: https://tidelift.com/badges/package/npm/eslint-plugin-react?style=flat

[package-url]: https://npmjs.org/package/eslint-plugin-react
[npm-version-svg]: https://versionbadg.es/jsx-eslint/eslint-plugin-react.svg

[actions-image]: https://img.shields.io/endpoint?url=https://github-actions-badge-u3jn4tfpocch.runkit.sh/jsx-eslint/eslint-plugin-react
[actions-url]: https://github.com/jsx-eslint/eslint-plugin-react/actions
