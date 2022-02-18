### 借鉴Gee(https://geektutu.com/post/gee.html)实现的MyGee
#### 主要功能:
##### 1.支持上下文(context)
##### 2.前缀树实现路由
##### 3.支持路由分组控制
##### 4.可以添加中间件
##### 5.支持使用golang内置html模板

#### 结构:
##### gee.go
```go
package gee

// HandlerFunc defines the request handler used by gee
type HandlerFunc func(*Context)

// Engine implement the interface of ServeHTTP
type Engine struct {
	*RouterGroup
	router        *router
	groups        []*RouterGroup     // store all groups
	htmlTemplates *template.Template // for html render
	funcMap       template.FuncMap   // for html render
}

type RouterGroup struct {
	prefix      string
	middlewares []HandlerFunc // support middleware
	engine      *Engine       // all groups share an Engine instance
}

// New is the constructor of gee.Engine
func New() *Engine

// Default use Logger() & Recovery() middlewares
func Default() *Engine

// Run defines the method to start a http server
func (engine *Engine) Run(addr string) (err error)

// Group is defined to create a new RouterGroup
// remember all groups share the same Engine instance
func (group *RouterGroup) Group(prefix string) *RouterGroup

// Use is defined to add middleware to the group
func (group *RouterGroup) Use(middlewares ...HandlerFunc)

// serve static files
func (group *RouterGroup) Static(relativePath string, root string)

// GET defines the method to add GET request
func (group *RouterGroup) GET(pattern string, handler HandlerFunc)

// POST defines the method to add POST request
func (group *RouterGroup) POST(pattern string, handler HandlerFunc)

// template
func (engine *Engine) SetFuncMap(funcMap template.FuncMap)
func (engine *Engine) LoadHTMLGlob(pattern string) 
```
##### context.go提供了上下文，封装 Request 和 Response ，提供对 JSON、HTML 等返回类型以及获取用户前端传入的参数的支持

##### router.go提供了路由与handler的对应，支持动态路由

##### logger.go与recovery.go实现了日志及恢复的中间件，默认web框架使用这两个中间件
