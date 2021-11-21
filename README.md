# antd-form

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build

```
# API 
| 参数           | 说明    |  类型  |   默认值  |
| --------      | -----  | :----: | :----: |
| mergePagination | 组合params,pagination返回请求接口所需要的格式     | Function(params, pagination)      |object|
| requestMethod   | api接口     |   Function    |-|
| params           | 筛选条件     |   object    |-|
| 其他           | 支持Pagination分页所有属性，详细见antd文档   |   -    |-|

# 事件
| 事件名称           | 说明    |  回调参数  |
| --------      | -----  | :----: |
| change | 页码改变的回调，参数是改变后的页码及每页条数    | Function(page, pageSize) |
| showSizeChange   | pageSize 变化的回调     |   Function(current, size)    |

# 说明
- 该组件支持分页功能，支持筛选，搜索，分页防抖功能，处理删除最后一页数据返回上一页的数据回现问题
