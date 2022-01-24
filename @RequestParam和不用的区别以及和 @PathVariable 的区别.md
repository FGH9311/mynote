## @RequestParam和不用的区别以及和 @PathVariable 的区别



 1.不使用，前端传递参数名必须和 方法形参名称一致

前台是id

@RequestMapping(“/test”)

public String test2( int id, int age, Date date, User user) { ... }



2. @RequestParam

2.1可以使用别名，前台是userId

@RequestMapping(“/test”)

public String test2(@RequestParam(“userId”) int id, int age, Date date, User user) { ... }

2.2 设置非必须参数 @RequestParam(required=false) int age



3. @RequestParam 和 @PathVariable区别

url 形式不同 

@RequestParam url : /test/getRegion/1

@PathVariable url ： /test/getRegion?id=1



4.@PathVariable

可以使用别名
@GetMapping("/getRegion/{id}")

public RegiongetRegion( @PathVariable(name ="id") Long primaryKey ){...}