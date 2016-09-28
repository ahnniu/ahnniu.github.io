---
layout: post
title: Rails Tips[1]　- RSpec
---

学习Rails的过程中，陆续整理一些笔记，供以后参考．


### 安装

修改Gemfile, 在development, test group中加上rspec-rails gem

> __NOTE__: 注意需要同时加到 development, test两个group, 我曾经仅仅添加到 test group中,但后续 rails generate model时, 不会生成rspec的测试文件.

```ruby
group :development, :test do
  gem 'rspec-rails', '~> 3.0'
end
```

下载并安装gem

```bash
$ bundle install
# Initialize the spec/ directory (where specs will reside) with:
$ rails generate rspec:install
```


参考：　[rspec-rails 文档](http://rspec.info/documentation/3.4/rspec-rails/)

### 如何用rspec 代替默认的 测试框架

#### 方法一 新建Rails App时传递参数

```bash
# 第一种方法: 传递 --skip-test-unit
$ rails new sample_app --skip-test-unit
# 第二种方法: 传递 -T 参数
$ rails new sample_app -T
```

后续在调用 `rails generate model`等命令都不会生成测试相关的, 后续可以手动添加. 如何手动添加, 见下文.


参考: 

- [stackoverflow - What is the difference in Rails commands: --no-test-framework, --skip-test-unit, and -T?](http://stackoverflow.com/questions/19105723/what-is-the-difference-in-rails-commands-no-test-framework-skip-test-unit)
- [stackoverflow - How can I tell Rails to use RSpec instead of test-unit when creating a new Rails app?](http://stackoverflow.com/questions/6728618/how-can-i-tell-rails-to-use-rspec-instead-of-test-unit-when-creating-a-new-rails)

#### 方法二 后续补救

如果新建App时候, 忘记传递 --skip-test-unit参数时, 怎么办?

```bash
# 首先删除 rails new时候, 默认生成的test目录
$ cd project/path
$ rm -rf test
# 参照安装部分, 修改Gemfile, 同时在development, test group中加入 rspec-rails后, bundle install
$ rails generate rspec:install
# 后续rails generate model时会自动生成rspec spec file
```


### 手动生成spec 

```bash
# will create a new spec file in spec/models/widget_spec.rb.
$ rails generate rspec:model widget
```

类似的还有: 代替 rspec:model中的model即可.

- scaffold
- model
- controller
- helper
- view
- mailer
- observer
- integration
- feature
- job

参考: [Rspec文档 - Generators](https://www.relishapp.com/rspec/rspec-rails/v/3-4/docs/generators)

#### 何时需要手动生成?

有时候, 在生成controller时, 不想生成默认的spec测试, 例如, controller 一般用 Request Spec / Feature Spec来测试, 默认生成的controller spec就不太合适.

```bash
# 传入 --no-test-framework 不会生成 rspec/test-unit的测试文件
$ rails generate controller StaticPages --no-test-framework
# 手动生成Feature Spec测试
$ rails generate rspec:feature static_pages
```


## Request spec vs Feature spec

相比单元测试, 这两个更多的是集成测试,或者说黑盒测试.

简单的说, 

个人认为Fature spec一般用于页面测试. 是完全的黑盒子, 是在模拟用户操作浏览器, 访问页面, 填充表单, 点击按钮.

而 Request spec则多用于API的测试, 使用http 的 get, post等动作, 当然, 也可以用填充表单的一些api, 我们这里, 更多的是在说如何更规范的使用rspec.

Rails rspec默认的集成测试是Request spec, 在 Rails tutorial中用的就是 Request spec.


参考: 

- [stackoverflow - RSpec: What is the difference between a feature and a request spec?](http://stackoverflow.com/questions/15173946/rspec-what-is-the-difference-between-a-feature-and-a-request-spec)
- [Rspec文档 - Request spec](https://www.relishapp.com/rspec/rspec-rails/v/3-4/docs/request-specs/request-spec)
- [Rspec文档 - Feature spec](https://www.relishapp.com/rspec/rspec-rails/v/3-4/docs/feature-specs/feature-spec)

##　参考

- [Rspec 入门指南(Better Specs { rspec guidelines with ruby })](http://betterspecs.org/zh_cn/)
- [很齐全的Rspec官方文档](https://www.relishapp.com/rspec/)
