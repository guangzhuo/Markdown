> 如何写成公用common 方法
1. 在`support>commands.js`写出公用的方法
2. 在`support>index.js`导入`import './commands'`

```
Cypress.Commands.add('login', () => {
    cy.visit('/')
    cy.get('[href="/login"]').click()
    cy.contains('使用验证码方式登录').click()
    cy.get('#phone').type('XXX')
    cy.get('#code').type('111111')
    cy.get('button[type="submit"]').contains('立即登录').click()
    cy.wait(2000)
    cy.log('登入成功');
})
```

> 环境变量设置

1. 创建`env`文件夹
2. 如：`cypress-env.json`

```
{
  "baseUrl":"http://172.168.22.6:3020/"
}
```
3. 在`package.json`创建命令：`"open:dev": "cypress open --config-file env/cypress-dev.json"`

> 合并报告
1. npm install --save-dev mochawesome mochawesome-merge mochawesome-report-generator
2. 在配置文件`cypress.json`

```
{
  ...,
  "reporter": "mochawesome",
  "reporterOptions": {
    "files":["mochawesome-report/mochawesome*.json"],
    "overwrite": false, 
    "html": false,
    "json": true
  }
}
```
3. 创建`scripts/sypress.js`

```
const cypress = require('cypress')
const marge = require('mochawesome-report-generator')
const { merge } = require('mochawesome-merge')
cypress.run().then(
  () => {
    generateReport()
  },
  error => {
    generateReport()
    console.error(error)
    process.exit(1)
  }
)



function generateReport(options) {
  console.log(options);
  return merge(options).then(report => marge.create(report, options))
}
```
4. `package.json`配置启动：

```
"delete:reports": "rm mochawesome-report/* || true",  
"cypress:report": "npm run delete:reports&&node scripts/cypress.js",
```


> 碰到的问题：

- 代码覆盖率报告与antd组件解构报错

```
原因：按需引入组件插件与cypress插件冲突，导致项目组件解析错误如：const {option} = Select;类型

解决方案：目前GitHub上 有人是推荐不解构antd组件,来编写项目（个人不推荐）
期待后续插件更新解决
```
