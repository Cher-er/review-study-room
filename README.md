# 数据库（库名 `study_room`）

## room

```sql
-- 自习室 entity
create table `room` (
    `id` int primary key auto_increment,
    `type` varchar(255),     -- 自习室类型
    `address` varchar(255),  -- 地址，如A103表示A楼1层03室
    `available` bool,        -- 自习室可用状态
    `open_start_time` time,  -- 一天中的开放时间
    `open_end_time` time     -- 一天中的结束时间
);
```



## seat

```sql
-- 座位 entity （自习室-座位：一对多关系）
create table `seat` (
    `id` int primary key auto_increment,
    `rid` int not null,  -- refer to room.id
    `occupied` bool,     -- 座位可用状态
    `type` tinyint,      -- 座位所在桌共有几个座位
    `charge` bool        -- 座位旁是否能充电
);
```



## user

```sql
-- 用户 entity （包括管理员、学生）
create table `user` (
    `id` int primary key auto_increment,
    `username` varchar(255) not null,  -- 用户名（用于登录）
    `password` varchar(255) not null,  -- 密码（存储md5加密后的结果）
    `nickname` varchar(255),           -- 昵称（用于显示，不设置则显示username）
    `permission` tinyint               -- 用户权限
                                       --   0：管理员
                                       --   1：学生
);
```



## record

```sql
-- 预约记录 （user和seat的关系表）
create table `record` (
    `id` int primary key auto_increment,
    `uid` int not null,       -- refer to user.id
    `sid` int not null,       -- refer to seat.id
    `reserve_time` datetime,  -- 预约成功的时间
    `register` boolean,       -- 是否签到
    `register_time` datetime  -- 签到时间
    `start_time` datetime,    -- 本次预约的开始时间
    `end_time` datetime       -- 本次预约的结束时间
);
```





# 功能

## 自习室和座位管理（管理员）

- 修改自习室的所有属性（manage-room.html）
- 修改座位的所有属性（manage-seat.html）



## 查看可用座位以及预约（学生）

- 查看自习室的所有可用座位（reserve.html）
- 指定自习室地址、座位数、是否能充电，查看可用座位（reserve.html）
- 预约某个可用座位（reserve.html）
- 查看自己的预约历史记录（history.html）





# 页面

## login.html

- 两个文本框用于输入用户名、密码
- “登录” 按钮，请求接口 login (post)，收到响应后
  - 若权限为 0，跳转到 manage.html
  - 若权限为 1，跳转到 reserve.html
- “注册” 按钮，跳转到 register.html



## register.html

- 三个文本框，用于输入用户名、密码、确认密码
- “注册” 按钮，请求接口 register (post)，收到响应后
  - 注册成功，弹出提示框，跳转到 login.html
  - 注册失败，弹出提示框，显示失败原因



## manage.html

- “管理自习室” 按钮，跳转到 manage-room.html
- “管理座位” 按钮，跳转到 manage-seat.html
- "注销" 按钮，跳转到 login.html



## manage-room.html

- 默认请求接口 room(get) 获取第一页的自习室信息
- 每行末尾 “修改” 按钮，点击后隐藏 “修改” “删除” 按钮，显示 “确认” “取消” 按钮
  - “确认” 按钮，请求接口 room (put)，收到响应后
    - 删除成功，请求接口 room (get) 刷新页面
    - 删除失败，弹出提示框显示原因
  - “取消” 按钮，重置该行座位信息，隐藏 “确认” “取消” 按钮，显示 “修改” “删除” 按钮
- 每行末尾 “删除” 按钮，点击后弹出提醒框，确认是否删除，包含 “确认” “取消” 按钮
  - “确认” 按钮，请求接口 room (delete)，收到响应后
    - 删除成功，请求接口 room (get) 刷新页面
    - 删除失败，弹出提示框，显示原因
  - “取消” 按钮，关闭提醒框
- 下拉框 “自习室楼号” “自习室层号” “自习室室号” “可用状态”
- “查询” 按钮，根据下拉框中的数据，请求符合条件的第一页自习室信息
- 分页列表展示所有自习室信息
  - 页号按钮、前进按钮、后退按钮，请求接口 room (get)，获取对应页的自习室信息（要考虑最近一次查询的查询条件）
- 新增自习室窗口，包含 自习室编号 文本框，开放时间、结束时间 时间选择器，和 “新增” 按钮
  - “新增” 按钮，请求接口 room (post) ，收到响应后
    - 新增成功，请求接口 room (get) 刷新页面
    - 新增失败，弹出提示框，显示原因



## manage-seat.html

- 默认请求接口 seat (get) 获取第一页的座位信息
- 每行末尾 “修改” 按钮，点击后隐藏 “修改” “删除” 按钮，显示 “确认” “取消” 按钮
  - “确认” 按钮，请求接口 seat (put)，收到响应后
    - 删除成功，请求接口 seat (get) 刷新页面
    - 删除失败，弹出提示框显示原因
  - “取消” 按钮，重置该行座位信息，隐藏 “确认” “取消” 按钮，显示 “修改” “删除” 按钮
- 每行末尾 “删除” 按钮，点击后弹出提醒框，确认是否删除，包含 “确认” “取消” 按钮
  - “确认” 按钮，请求接口 seat (delete)，收到响应后
    - 删除成功，请求接口 seat (get) 刷新页面
    - 删除失败，弹出提示框，显示原因
  - “取消” 按钮，关闭提醒框
- 下拉框 “自习室楼号” “自习室层号” “自习室室号” “座位数” “是否能充电”，时间选择器 “预约时间段”
- “查询” 按钮，根据下拉框、时间选择器中的数据，请求符合条件的第一页座位信息
- 分页列表展示所有座位信息
  - 页号按钮、前进按钮、后退按钮，请求接口 seat (get)，获取对应页的座位信息（要考虑最近一次查询的查询条件）
- 新增座位窗口，包含 自习室编号、连坐数 两个文本框，可充电 勾选框，和 “新增” 按钮
  - “新增” 按钮，请求接口 seat (post) ，收到响应后
    - 新增成功，请求接口 seat (get) 刷新页面
    - 新增失败，弹出提示框，显示原因



## reserve.html

- 默认请求接口 seat (get) 获取所有座位的第一页座位信息（包括座位的数据，和目前该座位剩余可预约时间的数据）
- 每行末尾 “预约” 按钮，点击后弹出提示框，包含目前剩余可预约时间 和  “预约时间段” 的时间选择器， “确认” “取消” 按钮
  - “确认” 按钮，请求接口 reserve (post)，收到响应后
    - 预约成功，弹出提示框，包含 “确认” 按钮，请求接口 seat (get) 刷新页面
    - 预约失败，弹出提示框，显示原因
  - “取消” 按钮，关闭提示框
- 下拉框 “自习室楼号” “自习室层号” “自习室室号” “座位数” “是否能充电” ，时间选择器 “期望预约时间段”
- “查询” 按钮，根据下拉框、时间选择器中的数据，请求符合条件的第一页座位信息
- 分页列表展示所有座位信息
  - 页号按钮、前进按钮、后退按钮，请求接口 seat (get)，获取对应页的座位信息（要考虑最近一次查询的查询条件）
- “历史记录” 按钮，跳转到 history.html



## history.html

- 默认请求接口 record (get) 获取第一页的历史记录信息
- 分页列表展示所有座位信息
  - 页号按钮、前进按钮、后退按钮，请求接口 record (get)，获取对应页的历史记录信息





# 接口

## login

### POST

- 请求参数：
  - username
  - password
- 业务逻辑
  1. 将 password 进行 md5 加密
  2. 根据 username、password 查询 user
  3. 不存在，则发送失败响应
  4. 存在，则发送成功响应
- 响应参数：
  - status：200 表示成功，404 表示未找到该用户
  - data：`User`，失败时为 None



## register

### POST

- 请求参数：
  - username
  - password
- 业务逻辑：
  1. 查询是否存在相同 username
  2. 存在，则发送失败响应，return
  3. 不存在，则将 password 进行 md5 加密，将 permission 设置为 1
  4. 根据 username、password、permission 插入 user
  5. 插入成功，发送成功响应
- 响应参数：
  - status：200 表示成功，409 表示用户已存在



## room

### GET

- 请求参数：
  - building
  - floor
  - room
  - enable
  - offset
  - count
- 业务逻辑：
  1. 将 building、floor、room 拼接为 address，若缺失某项，则在查询时使用 like
  2. 将 address、enable 放入 where 子句，offset、count 放入 limit 子句，查询 room 表
  3. 查询成功，发送成功响应
- 响应参数：
  - data：`List<Room>`

### PUT

- 请求参数：
  - json
    - id
    - address
    - enable
    - open_start_time
    - open_end_time
- 业务逻辑：
  1. 将 id 放入 where 子句，其余放入 set 子句，修改 room 表
  2. 修改成功，发送成功响应
- 响应参数：
  - status：200 表示修改成功

### DELETE

- 请求参数：

  - id

- 业务逻辑：

  1. 根据 id，执行删除

  2. 删除成功，发送删除响应

- 响应参数：

  - status：200 表示删除成功

### POST

- 请求参数：
  - json
    - address
    - open_start_time
    - open_end_time
- 业务逻辑：
  1. enable 默认为 true，插入 room 表
  2. 新增成功，发送成功响应
- 响应参数：
  - status：200 表示新增成功



## seat

### GET

- 请求参数：
  - building
  - floor
  - room
  - enable
  - type
  - charge
  - offset
  - count
  - start_time
  - end_time
- 业务逻辑：
  1. 将 building、floor、room 拼接为 address，若缺失某项，则在查询时使用 like
  2. 将 address、enable、type、charge 放入 where 子句，offset、count 放入 limit 子句，查询 seat 表
  3. start_time、end_time 为前端发送的期望预定时间，如果都为空，就需要查询 record 表，将所有座位的剩余可用时间查询出来，并以列表返回；如果 end_time 为空，需要查询 record 表，将所有座位在 start_time 之后的剩余可用时间查询出来，并以列表返回；如果 start_time 为空，需要查询 record 表，将所有座位在 end_time 之前的剩余可用时间查询出来，并以列表返回；如果都不为空，则查询 record 表，筛选出 start_time ~ end_time 之间没有预约记录的座位，并以列表返回。
  4. 查询成功，发送成功响应
- 响应参数：
  - data：`List<SeatWithRestTime>`
  
    > class SeatWithRestTime:
    >
    > ​    包含Seat的所有属性
    >
    > ​    额外的一个列表属性 rest_time: [(start1, end1), (start2, end2), (start3, end3), ......]，start1 ~ end1 表示第一个可用的预约时间，以此类推

### PUT

- 请求参数：
  - json
    - id
    - address
    - enable
    - type
    - charge
- 业务逻辑：
  1. 将 id 放入 where 子句，其余放入 set 子句，修改 seat 表
  2. 修改成功，发送成功响应
- 响应参数：
  - status：200 表示修改成功

### DELETE

- 请求参数：

  - id

- 业务逻辑：

  1. 根据 id，执行删除

  2. 删除成功，发送删除响应

- 响应参数：

  - status：200 表示删除成功

### POST

- 请求参数：
  - json
    - address
    - type
    - charge
- 业务逻辑：
  1. enable 默认为 true，插入 seat 表
  2. 新增成功，发送成功响应
- 响应参数：
  - status：200 表示新增成功



## reserve

### POST

- 请求参数：
  - uid
  - sid
  - start_time
  - end_time
- 业务逻辑：
  1. 根据 sid 查询座位信息，若 enable 为 false，则失败
  2. 根据 uid 查询 record 表，若存在时间段冲突的记录，则失败
  3. 获取此时时间，根据 uid、sid、start_time、end_time，插入 record 表
  4. 发送响应
- 响应参数：
  - status：200 表示成功；409 表示失败
  - data：`String`，失败原因





# SpringBoot和Vue之间如何通过信息码沟通

- SpringBoot：

  ```java
  @RestController
  public class MyController {
  
      @GetMapping("/api/resource")
      public ResponseEntity<String> getResource() {
          // 这里可以根据业务逻辑判断是否返回成功
          boolean success = true;
          
          if (success) {
              // 如果成功，返回信息码200和信息字符串"Success"
              return ResponseEntity.ok("Success");
          } else {
              // 如果失败，返回信息码404和信息字符串"Resource Not Found"
              return ResponseEntity.status(HttpStatus.NOT_FOUND).body("Resource Not Found");
          }
      }
  }
  ```

- Vue：

  ```javascript
  import axios from 'axios';
  
  axios.get('/api/resource')
    .then(response => {
      // 获取返回的信息码
      const statusCode = response.status;
      // 获取返回的信息字符串
      const message = response.data;
  
      // 根据返回的信息码和信息字符串进行相应的处理
      if (statusCode === 200) {
        console.log('Success:', message);
        // 处理成功的逻辑
      } else {
        console.log('Error:', message);
        // 处理失败的逻辑
      }
    })
    .catch(error => {
      // 获取错误信息
      console.error('Error:', error);
      // 处理错误的逻辑
    });
  ```

  

  
