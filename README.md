# フロントエンド入門





## アジェンダ



- フロントエンドロードマップ2018
- JavaScript
  - ECMAScriptとは
  - ES5 vs ES6
  - ブラウザサポート
  - ブラウザでJavaScriptを使ってみよう
  - RailsアプリでJavaScriptを導入してみよう
- jQuery
  - jQueryとは
  - jQueryを使ってみよう
  - RailsアプリでjQueryを導入してみよう
  - Ajax (非同期通信)
  - jQueryの問題点
- Node.jsについて
- パッケージマネージャ (npm/Yarn)
- Babel
- Webpack
- JSフレームワーク
  - React
  - Angular
  - Vue
- MPA vs SPA
- Rails + VueでMPAを作ってみよう
- Rails + VueでSPAを作ってみよう
  - ルーティング
  - 状態管理

(おまけ)

- タスクランナー
- 型チェック
- テストツール



## フロントエンドロードマップ2018



<https://codeburst.io/the-2018-web-developer-roadmap-826b1b806e8d>



## JavaScript





### ECMAScriptとは



### ブラウザサポート





http://kangax.github.io/compat-table/es6/



## Vanilla JavaScript

普通のJavaScriptのこと。Pure JavaScriptとも呼ばれることも。



### Lesson1: ブラウザでJavaScriptを使ってみよう



fusic.co.jp へアクセス、ブラウザのDevToolsを開いて、以下を実行してみよう！

```javascript
document.getElementsByTagName('h2')[0].innerText = 'hoge'
```



### Lesson2: RailsアプリでJavaScriptを導入してみよう (Asset Pipeline編)



#### 1. 環境構築

- /vanilla-jsへ移動
- `docker-compose up -d`



#### 2. Railsアプリ準備



- `docker-compose exec web bash`
- `bundle init`
- Gemfileの `gem "rails"` のコメントを外す
- `bundle install`
- `rails new .`
- `rails db:create` 
- `rails s -b 0.0.0.0`



#### 3. Todoアプリを作成



- `rails g scaffold Todo title:string body:string`
- `rails db:migrate`



#### 4. JavaScriptを使ってDOM要素を操作してみる



- app/assets/javascripts/ に `todos.js` を作成
- 以下コードを記述



```javascript
// DOMコンテンツの読み込みが終わった後に処理を実行
document.addEventListener("DOMContentLoaded", function(event) {

  // todoクラスの要素を取得
  var todos = document.getElementsByClassName("todo");

  // 各要素にクリックイベントを登録
  for (var i = 0; i < todos.length; i++) {
    todos[i].onclick = function (e) {
      this.style = 'display: none;'
    };
  }
});
```



app/views/todos/index.html.erb を編集



```html
<p id="notice"><%= notice %></p>

<h1>Todos</h1>

<table>
  <thead>
    <tr>
      <th>Title</th>
      <th>Body</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @todos.each do |todo| %> 
      <tr class="todo"> <%# <- 追加 %>
        <td><%= todo.title %></td>
        <td><%= todo.body %></td>
        <td><%= link_to 'Show', todo %></td>
        <td><%= link_to 'Edit', edit_todo_path(todo) %></td>
        <td><%= link_to 'Destroy', todo, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Todo', new_todo_path %>
```





## jQuery



### jQueryとは

JSライブラリの一つ。JavaScriptをシンプルに書けたり、便利メソッドが使える。



### Lesson3: jQueryを使ってみよう



<https://www.w3schools.com/jquery/>



### Lesson4: RailsアプリでjQueryを導入してみよう



#### jQuery導入

- Gemfileに `gem 'jquery-rails'` を追加、`bundle install`
- app/assets/javascripts/application.jsに `//= require jquery` を追加



### Challenge1: jQueryに書きかえてみよう

todos.jsをjQueryに書きかえください。

Lesson3とほぼ同じ記述でできます。



### Ajax (非同期通信)

"Asynchronous JavaScript and XML" の略。

簡単に言うとブラウザのページリロードなしでサーバーとの通信ができる技術。



## Lesson5: Ajaxを使って検索機能をつけてみる



todos_controller.rbを編集

```ruby
def index
    @todos = Todo.where('title LIKE ?', "%#{params['title']}%")
 end
```



todos.jsを編集



```js
// 検索
function search(el) {
  if (event.key === "Enter") {
    $.get("todos.json", { title: el.value }, data => {
      const tbody = $("tbody");
      tbody.empty();
      data.forEach(({ title, body, id }) => {
        tbody.append(`
          <tr class="todo">
            <td>${title}</td>
            <td>${body}</td>
            <td>
              <a href="/todos/${id}">Show</a>
            </td>
            <td>
              <a href="/todos/${id}/edit">Edit</a>
            </td>
            <td>
              <a data-confirm="Are you sure?" rel="nofollow" data-method="delete" href="/todos/${id}">Destroy</a>
            </td>
          </tr>
      `);
      });
    });
  }
}
```



todos/index.html.erbのtbodyタグ下に以下を追加



```html
<input type="text" onkeydown="search(this)">
```





## jQueryの問題点



- 大規模になってくるとコードの管理が難しくなる
- DOM操作が難しい
- 状態管理が難しい



## Node.jsについて



サーバサイドのJavaScript。

今回はサーバーサイドで使用するわけではなですが、パーケージ管理ツールを使うためにインストールしてあります。



## パッケージマネージャ (npm/Yarn)



Node.jsのpackageを管理するためのツール。

gem ≒ package

npm/Yarn ≒ Bundler



## Babel



ES6/ES7などで書かれたコードをES5に変換するトランスパイルツール。

これを使うことで、開発者は最新のJavaScript構文で書くことができ、本番環境で配布する際に、全ブラウザがサポートしているES5のソースコードへトランスパイルして配布することができます。



<https://babeljs.io/>



## Webpack



ビルドツール。JSフレームワークなど使う際には必須のツール。

以下のようなことができる。

- Vue/Angualrなどのフレームワークのコンパイル
- リント、テストなどのタスク実行
- Sass/SCSSなどのコンパイル、CSSファイルの読み込み管理
- Babel/TypeScriptなどのトランスパイル
- ローダーの実行

などができます。



他にもPercel, Rollupなどあります。



## MPA vs SPA

[https://scrapbox.io/vue-yawaraka/SPA%E3%81%A8MPA%E3%81%A3%E3%81%A6%E4%BD%95%E3%81%8C%E9%81%95%E3%81%86%E3%81%AE%EF%BC%9FSPA%E3%81%AB%E3%81%97%E3%81%9F%E3%81%BB%E3%81%86%E3%81%8C%E3%81%84%E3%81%84%EF%BC%9F](https://scrapbox.io/vue-yawaraka/SPAとMPAって何が違うの？SPAにしたほうがいい？)

