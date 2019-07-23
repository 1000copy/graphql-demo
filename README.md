## [graphql-demo](https://github.com/hemanth/graphql-demo).

A simple graphql-demo, which exposes an endPoint which can be quiered for books.

__Schema:__

```js
  type Book {
    id: ID!
    title: String!
    author: String!
    isbn: String!
    url: String!
  }

  type Query {
    allBooks: [Book!]!
  }

  type Mutation {
    createBook(title: String!, author: String!): Book
  }
```

__Query samples:__

```js
query {
  allBooks {
    author,
    title
  }
}
```

```js
query{
  allBooks(filter:{
    id:17
  }) {
    isbn
  }
}
```

```js
query{
  allBooks(filter:{
    id:17
  }) {
    isbn
  }
}
```

```js
    mutation{
      createBook(title:"vue.js book",author:"reco"){title}
    }
```

# GraphQL 入门

GraphQL 是一个用于 API 的查询语言。

## 定义GraphQL服务

定义一个服务，你需要做这样的事儿：

1. 定义类型 Schema，类型包括字段
2. 定义Schema上的每个字段提供解析函数


## 基于Apollo的可运行案例

假设我们有一个资源名字叫做Book，它有如下几个字段:

    名称  类型
    -----
    id   ID
    title string
    author string
    isbn   string
    url    string
  
我们系统提供用户查询Book和创建Book的能力。那么，首先我们需要定义此Schema：

  const typeDefs = gql`
    type Book {
      id: ID!
      title: String!
      author: String!
      isbn: String!
      url: String!
    }
    type Mutation {
      createBook(title: String!, author: String!): Book
    }

    type Query {
      allBooks(filter: bookFilter): [Book!]!
    }
    input bookFilter {
      id: ID
      title: String
      author: String
      isbn: String
      url: String
    }
  `;


首先，代码中有一个不太熟悉的语法：

    gql``

这个语法叫做哦`Tagged templates`。可以在模板字符串前放一个函数，然后此函数执行可以把一个模板字符串内的静态和动态部分分开作为参数传递到函数内。像是这样：

    function foo(staticParts, d1,d2) {
      console.log(staticParts, d1,d2);
    }
    foo`this is a ${42} test ${0}`
    // output : [ 'this is a ', ' test ', '' ] 42 0

这样的能力允许你剖析模板字面量，第一个参数是一个数组包含了字面量的静态部分，其他参数则是字面量的动态部分。

此语法特性，常常用来做DSL（领域特定语言）。ref to  https://2ality.com/2011/09/quasi-literals.html

模板字面量内的字符内容，就是一种领域语言，这里叫做GraphQL。

在GraphQL内可以定义类型，类型可以是Mutation、Query、自定义类型。

这里的自定义类型就是Book，可以定义此类型的构成，如下：

    type Book {
      id: ID!
      title: String!
      author: String!
      isbn: String!
      url: String!
    }
    
说明Book类型是有id、title、author、isbn、url字段组成，每个字段可以标记类型，类型可以是ID、String等，这样的简单类型被统称为Scalar类型，Scalar的全部清单如下：

    Int − Signed 32-bit Integer
    Float − Signed double precision floating point value
    String − UTF - 8-character sequence
    Boolean − True or false
    ID − A unique identifier, often used as a unique identifier to fetch an object or as the key for a cache.

。类型后加入！表示字段是必须的，不写的话就是可选的。

type Book 是一个对象类型，对象类型看起来是这样的：

    type object_type_name
    {
       field1: data_type
       field2:data_type 
       ....
       fieldn:data_type
    }

变量加入[]就是对应变量的数组，像是这样： [Book]

Mutation类型，指的是对资源进行修改的类型，包括创建、修改、删除。Mutation类型是一个根类型。这里定义的是一个对资源的创建：

  createBook(title: String!, author: String!): Book

The syntax for defining a Mutation type is given below −

    type Mutation {
       field1: data_type
       field2(param1:data_type,param2:data_type,...paramN:data_type):data_type 
    }

For example, we can define a mutation type to add a new Student as below −

    type Mutation {
       addStudent(firstName: String, lastName: String): Student
    }

创建Book资源可以传递两个参数，类型为String!，其中的叹号，表示此参数是必须的。

在GraphQL内还可以定义Query类型，Query Type，A GraphQL query is used to fetch data.这里定义的是一个对资源的查询：

  allBooks(filter: bookFilter): [Book!]!

查询名字为allBooks，返回一个Book数组。查询的参数为filter，类型有bookFilter定义：

    input bookFilter {
      id: ID
      title: String
      author: String
      isbn: String
      url: String
    }

现在，就可以输入这样的命令，执行查询：

    query{
      allBooks{
        id
      }
    }

返回的是一个包括Book的数组，数字内每一个Book都含有id字段。

    {"data": {
      "allBooks": [
        {
          "id": "1"
        },
    }

或者，就可以输入这样的语言，执行查询：

    query{
      allBooks{
        id
        title
      }
    }

返回的是一个包括Book的数组，数字内每一个Book都含有id、title字段。


如果想要条件查询字段的话，可以使用指令：

    You can pass a variable to your query and use a Directive to fetch a field conditionally, like name @include(if: $shouldFetchName).

    See the docs for Directives.

传参也是允许的，语法如下：

    query{
      allBooks(filter:{id:17}) {
        isbn
      }
    }
返回如下：

    {
      "data": {
        "allBooks": [
          {
            "id": "1"
          }
        ]
      }
    }

对应resolvers写法是这样的：

    Query: {
      allBooks: (root, {filter}) => {
        // code here
      }
    }


创建一本书，可以使用如下的查询语言：

    mutation{
      createBook(title:"vue.js book",author:"reco"){title}
    }

会返回一本新的书：

    {
      "data": {
        "createBook": {
          "title": "vue.js book"
        }
      }
    }


具体仓库：google : graphql-demo

## 带有关联数据的例子

考虑一个博客系统，数据需要有Post和Comment两种类型。其中Post：


    const typeDefs = gql`
      type Post {
        id: ID!
        title: String!
        content:String!
        author: String!
        comments: [comment!]!
        published: String!
      }
      type Comment{
        id: ID!
        content: String!
        author: String!
        published: String!
      }
      type Mutation {
        createPost(title: String!, author: String!,content: String!): Post
        commentPost(id:ID!,content:String!,auther:String!):Comment
        deletePost(id:ID!):Boolean
        updatePost(id:ID!,title:String!,content:String!):Post
      }
      type Query {
        allPost(filter: postFilter): [Post!]!
      }
      input postFilter {
        id: ID
        title: String
        author: String
      }
    `;

考虑一个单据Bill和一个单据明细BillDetail。


## 如何实现Like查询？

The short answer is: you don't.

The longer answer is that you have to write that code yourself. GraphQL isn't a database query language like SQL, it's an application query language. What that means is that GraphQL won't let you write arbitrary queries out of the box. It will only support the types of queries defined in your GraphQL schema.

If you want to be able to write a query that contains like, you have to

a) declare that in the schema, and b) write a resolve function that fetches the data

For example, you could have this schema:

    type Query {
      users(firstName: String!): [User]
    }
    type User {
      firstName: String
      lastName: String
    }

You would have to define the following resolve function for the users field:

    {
      Query: {
        users(root, args){
          return sql.raw('SELECT * FROM `users` WHERE `firstName` LIKE ?', args.firstName);
        }
      }
    }

And finally write this query to get a list of firstName and lastName of all the users that match your search criteria:

    {
      users(firstName: 'jason%'){
        firstName
        lastName
      }
    } 

Here's a post I wrote a while ago that clarifies some of the concepts around GraphQL: https://medium.com/apollo-stack/how-do-i-graphql-2fcabfc94a01

And here's a post that explains the interplay between GraphQL schemas and resolve functions: https://medium.com/apollo-stack/graphql-explained-5844742f195e

很好的教程： https://www.tutorialspoint.com/graphql/index.htm

  
[继续阅读 →查询和变更](/learn/queries)
