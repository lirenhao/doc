## 创建一个react的例子
### 使用
- react-start-kit模板

### 创建步骤
- 创建项目

>使用WebStrom创建一个Entry Project

- 配置项目框架

>1、将[react-start-kit](https://github.com/kriasoft/react-starter-kit.git)根目录下的package.json文件和tools文件拷贝到新建项目的根目录<br/>
>2、在控制台中输入'npm install'运行安装node控件<br/>
>3、运行'npm install jquery --save'安装jQuery控件<br/>
>4、运行'npm install marked --save'安装marked控件

- 创建项目目录及文件
>1、在根目录下创建src目录<br/>
>2、在src目录下创建client.js文件<br/>
>3、在src目录下创建server.js文件<br/>
>3、在src目录下创建content文件夹<br/>
>4、在src目录下创建public文件夹<br/>
>5、在src目录下的public文件夹下创建index.html文件

- 创建Comment控件文件
>1、在src目录下创建Comment.js文件<br/>
>2、在Comment.js中引入所需控件
```base
import React from 'react';
import Marked from 'marked';
```
>3、创建Comment控件
```base
var Comment = React.createClass({
    rawMarkup: function () {
        var rawMarkup = Marked(this.props.children.toString(), {sanitize: true});
        return { __html: rawMarkup };
    },
    render: function () {
        return (
            <div className="comment">
                <h2 className="commentAuthor">
                    {this.props.author}
                </h2>
                <span dangerouslySetInnerHTML={this.rawMarkup()} />
            </div>
        );
    }
});
```
>4、输出Comment控件
```base
export default Comment;
```

- 创建CommentFrom控件文件
>1、在src目录下创建CommentFrom.js文件<br/>
>2、在CommentFrom.js中引入所需的控件
```base
import React from 'react';
```
>3、创建CommentFrom控件
```base
var CommentFrom = React.createClass({
    handleSubmit: function (event) {
        event.preventDefault();
        var author = this.state.author.trim();
        var text = this.state.text.trim();
        if (!text || !author) {
            return;
        }
        this.props.onCommentSubmit({author: author, text: text});
        this.setState({author: '', text: ''});
    },
    handleAuthorChange: function (event) {
        this.setState({author: event.target.value});
    },
    handleTextChange: function (event) {
        this.setState({text: event.target.value});
    },
    getInitialState: function () {
        return {author: '', text: ''};
    },
    render: function () {
        return (
            <form className="commentForm" onSubmit={this.handleSubmit}>
                <input type="text" onChange={this.handleAuthorChange}
                       placeholder="Your name"
                       value={this.state.author}/>
                <input type="text" onChange={this.handleTextChange}
                       placeholder="Say something..."
                       value={this.state.text}/>
                <input type="submit" value="Post"/>
            </form>
        );
    }
});
```
>4、到出CommentFrom控件
```base
export default CommentFrom;
```
- 创建CommentList控件文件
>1、在src目录下创建CommentList.js文件<br/>
>2、在CommentList.js中引入所需的控件
```base
import React from 'react';
import Comment from './Comment.js';
```
>3、创建CommentList控件
```base
var CommentList = React.createClass({
    render: function () {
        var commentNodes = this.props.data.map(function (comment) {
            return (
                <Comment author={comment.author} key={comment.id}>
                    {comment.text}
                </Comment>
            );
        });
        return (
            <div className="commentList">
                {commentNodes}
            </div>
        );
    }
});
```
>4、到出CommentList控件
```base
export default CommentList;
```

- 创建CommentBox控件文件
>1、在src目录下创建CommentBox.js文件<br/>
>2、在CommentBox.js中引入所需的控件
```base
import React from 'react';
import $ from 'jquery';
import CommentList from './CommentList.js';
import CommentFrom from './CommentFrom.js';
```
>3、创建CommentBox控件
```base
var CommentBox = React.createClass({
    handleCommentSubmit: function (comment) {
        var comments = this.state.data;
        comment.id = Date.now();
        var newComments = comments.concat([comment]);
        this.setState({data: newComments});
        $.ajax({
            url: this.props.url,
            dataType: 'json',
            type: 'POST',
            data: comment,
            success: function (data) {
                this.setState({data: data});
            }.bind(this),
            error: function (xhr, status, err) {
                this.setState({data: comments});
                console.error(this.props.url, status, err.toString());
            }.bind(this)
        });
    },
    getInitialState: function () {
        return {data: []};
    },
    loadCommentsFromServer: function () {
        $.ajax({
            url: this.props.url,
            dataType: 'json',
            cache: false,
            success: function (data) {
                this.setState({data: data});
            }.bind(this),
            error: function (xhr, status, err) {
                console.error(this.props.url, status, err.toString());
            }.bind(this)
        });
    },
    componentDidMount: function () {
        this.loadCommentsFromServer();
        setInterval(this.loadCommentsFromServer, this.props.pollInterval);
    },
    render: function () {
        return (
            <div className="commentBox">
                <h1>Comments</h1>
                <CommentList data={this.state.data}/>
                <CommentFrom onCommentSubmit={this.handleCommentSubmit}/>
            </div>
        );
    }
});
```
>4、到出CommentBox控件
```base
export default CommentBox;
```

- 在client中挂接节点
>1、在client.js中引入所需控件
```base
import React from 'react';
import ReactDOM from 'react-dom';
import CommentBox from './CommentBox.js';
import $ from 'jquery';
```
>2、挂接CommentBox组件
```base
$().ready(() => {
    ReactDOM.render(
        <CommentBox url="/api/comments" pollInterval={2000} />,
        document.getElementById('content')
    );
});
```

- 创建html节点
>1、在index.html的head中引入编译后的js
```base
<script src="./assets/main.js"/>
```
>2、在index.html中创建节点
```base
<div id="content"></div>
```

- 修改server.js文件 
>1、在server.js中引用引入所需控件
```base
import express from 'express';
import path from 'path';
import bodyParser from 'body-parser';
```
>2、添加端口监听和默认资源路径
```base
var port = 3000;
var app = express();
app.use(express.static(path.join(__dirname, 'public')));
app.listen(port, () => {
    console.log('The server is running at http://localhost:${port}/');
});
```
>3、添加资源访问路径及数据
```base
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true}));
var data = [
    {id: 1, author: "Pete Hunt", text: "This is one comment"},
    {id: 2, author: "Jordan Walke", text: "This is *another* comment"}
];
app.get('/api/comments', (req, resp) => {
    resp.json(data);
});
app.post('/api/comments', (req, resp) => {
    data.push(req.body);
    resp.json(data);
});
```

- 启动服务

>控制台输入以下命令启动
```base
npm start
```
