后台管理开发要点
===========

NOTE:后台开发，各功能简要逻辑就是“增删改查”，开发分为两部分， 后台逻辑+前台页面数据展示。

我们以Admin信息为例来说明， 实现Admin的增删改查。

后台逻辑开发
-------

后台功能架构基本是Spring MVC三层结构。 

Controller中定义接收前端协议数据，调用后端业务逻辑。
Service中处理业务逻辑实现，需要数据访问调用Dao层数据访问接口。
Dao层做数据访问逻辑，结合mybatis 的mapper实现sql语句，做数据的查询、插入、更新和删除操作。

1、Controller层

在com.u8x.controller包名下，新建一个AdminController，用来定义admin相关的增删改查协议

```

@Controller
@RequestMapping("/mgr/admin")
public class AdminController {

    private static final XLogger logger = XLogger.getLogger(AdminController.class);

    @Autowired
    private AdminService adminService;


    @RequestMapping(value = "/all", method = RequestMethod.GET)
    @ResponseBody
    ListView getAllAdmins( String username,
                           String adminRoleName,
                           HttpSession session){

        ListView<Admin> view = new ListView<Admin>();

        List<Admin> admins = adminService.getAdmins(username, adminRoleName);

        return view.fromList(admins);

    }


    @RequestMapping(value = "/save", method = RequestMethod.POST)
    @ResponseBody
    ResponseView saveAdmin(@ModelAttribute Admin admin){
        ResponseView view = new ResponseView();
        if(admin.getId() == null || admin.getId().equals(0)){
            //添加新管理员
            Admin exists = adminService.getAdminByName(admin.getUsername());
            if(exists != null){
                return view.failure(Consts.Tips.UserNameExists);
            }
            adminService.insertAdmin(admin);
        }else{
            //编辑已有管理员
            Admin exists = adminService.getAdminById(admin.getId());
            if(exists == null){
                return view.failure(Consts.Tips.FailMsg);
            }
            Admin nameExists = adminService.getAdminByName(admin.getUsername());
            if(nameExists != null && !nameExists.getId().equals(admin.getId())){
                return view.failure(Consts.Tips.UserNameExists);
            }
            exists.setUsername(admin.getUsername());
            exists.setAdminRoleID(admin.getAdminRoleID());
            exists.setAdminRoleName(admin.getAdminRoleName());

            if(admin.getPassword() != null && admin.getPassword().trim().length() > 0){
                exists.setPassword(admin.getPassword());
            }

            adminService.updateAdmin(exists);
        }

        return view.success();
    }

    @RequestMapping(value = "/delete", method = RequestMethod.POST)
    @ResponseBody
    ResponseView removeAdmin(@RequestParam(required = true) int id){
        ResponseView view = new ResponseView();
        Admin exists = adminService.getAdminById(id);
        if(exists == null){
            return view.failure(Consts.Tips.AdminNotExists);
        }
        adminService.deleteAdmin(id);
        return view.success();
    }

}

1、类上面统一加注解

@Controller
@RequestMapping("/mgr/admin")   //这里相对地址，统一用 /mgr/当前操作对象名称

2、类里面根据需要， 定义查询、增加/修改、删除接口。方法上面统一用注解

@RequestMapping(value = "/all", method = RequestMethod.GET)     //查询用GET方式；保存和删除用POST方式。
@ResponseBody

3、查询接口，返回对象统一用ListView

4、添加和修改可以用一个接口也可以用两个接口来处理。一个接口处理，根据前端传来的值里面有无主键ID来决定当前是添加还是修改。

5、删除，根据主键唯一ID来删除。

6、类中用@Autowired注解，自动注入AdminService 业务接口。

```

2、Service层

在com.u8x.services包名下，新建一个AdminService，用来定义admin相关的增删改查实际业务逻辑

```
@Service
public class AdminService {

    @Autowired
    private AdminDao adminDao;

    public Admin getAdminByName(String name){

        return adminDao.getAdminByName(name);
    }

    public Admin getAdminById(Integer id){
        return adminDao.getAdminById(id);
    }

    public List<Admin> getAllAdmins(){

        return adminDao.getAllAdmins();
    }

    public List<Admin> getAdmins(String username, String adminRoleName){
        return adminDao.getAdmins(username, adminRoleName);
    }

    public void insertAdmin(Admin admin){
        adminDao.insertAdmin(admin);
    }

    public void updateAdmin(Admin admin){
        adminDao.updateAdmin(admin);
    }

    public void deleteAdmin(int id){
        adminDao.deleteAdmin(id);
    }

    public List<AdminRole> getAllAdminRoles(){
        return adminDao.getAllAdminRoles();
    }

}

1、类上面用注解

@Service

2、类里面用@Autowired自动注入AdminDao数据操作接口

3、后台管理类系统，业务逻辑一般就是增删改查，所以一般业务层仅仅是调用数据访问层操作数据。

```

3、Dao层

在com.u8x.dao包名下，新建一个AdminDao，用来定义admin相关的数据操作接口。

```
@Repository
@Transactional
public class AdminDao {

    @Autowired
    private AdminMapper adminMapper;

    @Autowired
    private AdminRoleMapper adminRoleMapper;

    public Admin getAdminByName(String name){
        return adminMapper.getAdminByName(name);
    }

    public Admin getAdminById(Integer id){
        return adminMapper.getAdminById(id);
    }

    public List<Admin> getAdmins(String username, String adminRoleName){
        return adminMapper.getAdmins(username, adminRoleName);
    }

    public List<Admin> getAllAdmins(){
        return adminMapper.getAllAdmins();
    }

    public List<AdminRole> getAllAdminRoles(){
        return adminRoleMapper.getAllAdminRoles();
    }

    public void insertAdmin(Admin admin){
        adminMapper.insertAdmin(admin);
    }

    public void updateAdmin(Admin admin){
        adminMapper.updateAdmin(admin);
    }

    public void deleteAdmin(int id){
        adminMapper.deleteAdmin(id);
    }

}

1、类上面用注解

@Repository         
@Transactional      //声明数据库事务支持

2、采用@Autowired注解自动注入AdminMapper， 采用mybatis组件， AdminMapper接口中，实现操作数据的sql语句。

```

在com.u8x.dao.mapper目录下，创建AdminMapper接口。

```
@Mapper
public interface AdminMapper {

    @Insert("INSERT INTO `admin`(`username`,`password`,`adminRoleID`,`adminRoleName`) VALUES(#{username},#{password},#{adminRoleID},#{adminRoleName})")
    void insertAdmin(Admin admin);

    @Update("UPDATE `admin` SET username=#{username},password=#{password},adminRoleID=#{adminRoleID},adminRoleName=#{adminRoleName} WHERE `id`=#{id}")
    void updateAdmin(Admin admin);

    @Select("SELECT * FROM `admin` WHERE username = #{username}")
    Admin getAdminByName(@Param("username") String username);

    @Select("SELECT * FROM `admin` WHERE id = #{id}")
    Admin getAdminById(@Param("id") Integer id);

    @Select("SELECT * FROM `admin` ")
    List<Admin> getAllAdmins();

    @SelectProvider(type = AdminSqlProvider.class, method = "searchAdmins")
    List<Admin> getAdmins(@Param("username") String username, @Param("adminRoleName") String adminRoleName);

    @Delete("DELETE FROM `admin` WHERE `id`=#{id}")
    void deleteAdmin(@Param("id") int id);

}


1、类上面用注解

@Mapper

2、插入用@Insert注解(插入最好写全列名)，更新用@Update，删除用@Delete注解。查询用@Select注解。

3、后台多项参数动态查询的时候， 需要用到@SelectProvider注解。 

在com.u8x.dao.mapper.provider目录下，定义生成SQL语句的方法，在Mapper中用@SelectProvider来指定。

比如后台管理员管理中，可以根据用户名和角色名称来随意组合查询。这里就用了动态查询语句拼装。

```

前台逻辑开发
-------

前台页面显示， 核心的逻辑， 就是通过http请求访问后端数据(一般都是json格式数据)，然后将获取到的数据进行展示。主要用到的技术，就是html,css,js以及jquery库等。

前端后台管理页面框架用的是[AdminLte](https://adminlte.io/themes/AdminLTE/index2.html)，该框架中UI表现效果基于Bootstrap框架实现。

前端中，我们主要用到的组件，包括：datatable(数据列表)、modal弹窗(编辑界面、提示框等)等


1、基础知识

1.1 发送http请求

http请求post方式：

```
        var username = "test";
        var password = "test111";
        $.post('admin/roles',{username:username,password:password}, function (result) {

            if(result != null && result.code == 0){
                var lst = result.data;

            }


        },'json');

1、第一个参数：请求url地址

2、第二个参数：请求参数列表（key对应后端协议参数名称）

3、请求结果回调处理函数

4、协议参数格式

```

http请求get方式：

```
        var username = "test";
        var password = "test111";
        $.get('admin/roles',{username:username,password:password}, function (result) {

            if(result != null && result.code == 0){
                var lst = result.data;

            }


        },'json');

1、第一个参数：请求url地址

2、第二个参数：请求参数列表（key对应后端协议参数名称）

3、请求结果回调处理函数

4、协议参数格式

```

1.2 jquery 控件选择

jquery中，我们需要对html文档中某个节点控件进行选择，可以使用jquery节点选择器。常用的比如：


```
根据id选取输入框中的值：

<input type="text" class="form-control" id="username" name="username" placeholder="用户名" maxlength="20">

这个输入框我们定义了id为 username

在js代码中，我们想要获取到这个input输入框中输入的值，那么我们可以这么获取：

$("#username").val();

我们想在js代码中给这个输入框赋值，那么可以这么做：

$("#username").val("new val");

```

```
获取所有带data-btn-type属性的button组件：

<button type="button" class="btn btn-default" data-btn-type="add">新增</button>
<button type="button" class="btn btn-default" data-btn-type="edit" >编辑</button>
<button type="button" class="btn btn-default" data-btn-type="delete">删除</button>

这个三个button我们都加上了data-btn-type属性。

在js代码中，我们想要获取到这个三个button，监听他们的点击事件，我们可以这样做：

$('button[data-btn-type]').click(function() {

    var action = $(this).attr('data-btn-type'); //$(this) 就是指当前选择的这个button控件

    switch(action){
        case "add":

            break;
        case "edit":

            break;

        case "delete":

            break;
    }

});

```

2、页面编写

在resources/plublic目录下，新建一个amdins.html，用来展示管理员数据，和处理增删改查操作。然后在index.html中，左边菜单栏中，管理员下面，将页面跳转设置为admins.html

```
<li><a href="admins.html" target="menuFrame"><i class="fa fa-circle-o"></i> 管理员</a></li>

1、href属性指定，点击“管理员”菜单，所跳转的页面。

```

2.1 引用css和js文件。

在head标签中，引用必要的css文件（可以从admins.html中拷贝）

部分js文件，写在文档内容下面，这样一定程度上优化文档的载入速度

2.2 展示数据列表

在body中，定义需要展示数据的节点。这里我们用datatable数据列表展示管理员列表数据。

```
<body class="hold-transition skin-blue sidebar-mini">

<div class="row">
    <div class="col-xs-12">

        <div class="box">
            <!--<div class="box-header">-->
                <!--<h3 class="box-title">Data Table With Full Features</h3>-->
            <!--</div>-->
            <!-- /.box-header -->

            <div class="dataTables_filter" id="searchDiv" style="margin-right: 10px;">

                <div class="btn-group" style="float: right;padding-top: 10px;">
                    <button type="button" class="btn btn-default" data-btn-type="add">新增</button>
                    <button type="button" class="btn btn-default" data-btn-type="edit" >编辑</button>
                    <button type="button" class="btn btn-default" data-btn-type="delete">删除</button>
                </div>

                <div class="btn-group" style="float:right;padding-top: 10px;margin-right: 10px;">
                    <button type="button" class="btn btn-primary" data-btn-type="select">查询</button>
                    <button type="button" class="btn btn-default" data-btn-type="reset">重置</button>
                </div>

                <div style="float: right;padding-top: 10px;margin-right: 5px;display: block;">
                    <div style="float: left;margin-right: 5px;"><input placeholder="请输入用户名" id="searchName" class="form-control" type="search" likeOption="true" /></div>
                    <div style="float:right;"><input placeholder="请输入角色名称" id="searchRoleName" class="form-control" type="search" likeOption="true" /></div>

                </div>
            </div>

            <div class="box-body">
                <table id="example2" class="table table-bordered table-striped table-hover">
                    <thead>
                    <tr>
                        <th>管理员ID</th>
                        <th>名称</th>
                        <th>角色ID</th>
                        <th>角色名称</th>
                    </tr>
                    </thead>

                </table>
            </div>
            <!-- /.box-body -->
        </div>
        <!-- /.box -->
    </div>
    <!-- /.col -->
</div>
</body>

1、body上面的class属性，指定当前页面样式，这些是adminlte中定义的样式

2、dataTables_filter节点中，定义了datatable上面的操作菜单。 这里有查询框、查询重置、新增、编辑和删除等控件。

3、box-body节点中，定义当前用于数据展示的节点。 这些都是adminlte中定义的结构。 可以参考adminlte的demo或者文档。

4、table中，我们在thead中指定了表头显示的列名称。table我们加了一个id：example2。js中我们根据这个id来定位这个table

```

然后，我们需要在页面载入进来的时候， 访问后端数据，去加载当前所有的管理员数据，并将数据展示到上面box-body中。

```

    $(function () {

        var table = $('#example2').DataTable({
            select: {
                style: 'single'
            },
            "ajax": {
                url:'admin/all',
                type:'GET'
            },
            columns: [
                { data: "id" },
                { data: "username" },
                { data: "adminRoleID" },
                { data: "adminRoleName" }
            ],
            "searching":false,
            "language": {
                "lengthMenu": "显示 _MENU_ 条记录",
                "zeroRecords": "没有查到任何记录",
                "info": "_PAGE_ / _PAGES_",
                "infoEmpty": "没有任何记录",
                "infoFiltered": "(总记录：_MAX_ )",
                "loadingRecords": "正在加载...",
                "processing":     "正在处理...",
                "search":         "搜索:",
                "paginate": {
                    "first":      "首页",
                    "last":       "尾页",
                    "next":       "下一页",
                    "previous":   "上一页"
                },
                "aria": {
                    "sortAscending":  ": 递增排序",
                    "sortDescending": ": 递减排序"
                },
                "select": {
                    "rows": "已选中%d行"
                }
            }
        });

    });

1、$(function())块，是在html文档加载完毕之后执行逻辑的地方。

2、调用$('#example2').DataTable方法，将文档中上面那个table控件指定为datatable控件，并进行相关设置。

3、select属性： 指定当前表格，是单选。

4、ajax属性：指定当前表格数据源是远程地址。 这里通过http get方式访问后端admin/all查询接口

5、columns属性：指定远程地址返回的数据源中，各个需要展示的字段。

6、searching属性：指定是否显示默认的搜索框

7、language属性：这里对datatable中一些显示的文字或者提示，进行汉化。

```

好了，这样你打开之后界面之后， 这段js会立马执行。 他会访问admin/all指定的后端协议接口获取数据，并将数据按照column中指定的字段进行显示。


2.3 处理添加/编辑界面

添加新管理员和编辑管理员界面，是同一个界面。 唯一的区别就是， 点击编辑之前，要选中一条记录，然后将选中的管理员记录的数据，显示在编辑界面的各个字段中。

adminlte中，提供了modal对话框，我们采用modal对话框来展示添加和编辑界面。

在上面文档的文档中class="row"的div同级节点下面，增加一个modal对话框节点：

```

<div class="modal fade" id="myModal" role="dialog" aria-labelledby="addTitle">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></button>
                <h4 class="modal-title" id="addTitle">管理员</h4>
            </div>
            <div class="modal-body">
                <div class="box box-primary">
                    <div id="errBlock">
                    </div>

                    <form class="form-horizontal" id="fm">
                        <div class="box-body">
                            <input type="text" class="form-control" type="hidden" style="display: none;" id="id" name="id">
                            <div class="form-group">
                                <label for="username" class="col-sm-2 control-label" data-toggle="tooltip">用户名</label>

                                <div class="col-sm-10">
                                    <input type="text" class="form-control" id="username" name="username" placeholder="用户名" maxlength="20">
                                </div>
                            </div>
                            <div class="form-group">
                                <label for="password" class="col-sm-2 control-label">密   码</label>

                                <div class="col-sm-10">
                                    <input type="password" class="form-control" id="password" name="password" placeholder="密码" maxlength="20">
                                </div>
                            </div>
                            <div class="form-group">
                                <label for="password2" class="col-sm-2 control-label">重复密码</label>

                                <div class="col-sm-10">
                                    <input type="password" class="form-control" id="password2" name="password2" placeholder="密码" maxlength="20">
                                </div>
                            </div>

                            <div class="form-group">
                                <label for="adminRole" class="col-sm-2 control-label">权限角色</label>
                                <div class="col-sm-10">
                                    <select class="form-control select2 adminRoleAjax" style="width: 100%;" id="adminRole" name="adminRoleID">
                                    </select>
                                </div>

                            </div>

                        </div>
                        <!-- /.box-body -->
                        <div class="box-footer">
                            <button type="button" class="btn btn-default" data-dismiss="modal">取  消</button>
                            <button type="button" class="btn btn-primary pull-right" onclick="saveAdmin();">保  存</button>
                        </div>
                        <!-- /.box-footer -->
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>

1、modal组件的节点格式，可以参考adminlte的文档或者demo。我们为该modal对话框，定义一个id为myModal，方便js中定位

2、modal-title节点指定当前编辑框的标题

3、form控件下面定义当前需要的表单控件。里面不外乎定义一些input，select，checkbox等表单控件

4、表单控件注意下，每个表单控件的name属性，必须指定和后端协议接口实体类中变量名称一致。

5、表单控件可以根据需要，增加id属性，方便在js代码中定位。

6、box-footer节点中，定义两个button， 保存和取消。 点击取消会关闭对话框（直接加上data-dismiss="modal"即可）；点击保存，执行文档下面定义的saveAdmin这个js函数。

```

定义了添加和编辑界面，我们现在要做的就是监听上面表格文档中定义的新增和编辑按钮事件，点击按钮的时候，弹出这个编辑界面。

```
$('button[data-btn-type]').click(function() {

    var action = $(this).attr('data-btn-type');
    switch (action){
        case 'add':
            requestRoles(0);
            $(':input','#myModal')
                .not(':button, :submit, :reset')
                .val('')
                .removeAttr('checked')
                .removeAttr('selected');
            $('#myModal').modal('show');
            break;

        case 'edit':
            var data = table.row('.selected').data();
            if(data == null){
                showTips("请先选择一条要编辑的记录");
                return;
            }
            $("#id").val(data.id);
            $("#username").val(data.username);
            requestRoles(data.adminRoleID);
            
            $('#myModal').modal('show');
            break;

        case 'delete':
            var data = table.row('.selected').data();
            if(data == null){
                showTips("请先选择一条要删除的记录");
                return;
            }

            showConfirm("确定要删除该记录吗？（操作不可恢复）", function () {
                deleteAdmin(data.id);
            });

            break;

        case 'reset':
            $("#searchName").val("");
            $("#searchRoleName").val("");
            break;

        case 'select':
            search();
            break;
    }

});

1、按照上面我们说的，我们监听所有有data-btn-type属性的按钮。然后根据action来区分当前是哪个按钮。

2、$('#myModal').modal('show'); 调用该方法，即可显示该编辑界面。对于新增，我们可以直接调用。

3、编辑的话，我们需要先判定，当前是否选中了一行数据。通过table.row('.selected').data() 不为空来判定。
然后将当前选中的数据填充到编辑界面上的控件中。然后再调用$('#myModal').modal('show');将对话框显示出来。

4、modal对话框会缓存上一次数据，导致第二次打开界面，里面有上一次数据。所以add中显示对话框之前，先把对话框中所有控件数据清除。

```

2.3 处理删除逻辑

上面可以看到， 当点击删除按钮时，我们也需要判断当前是否选中了一行。 选中之后， 直接调用deleteAdmin函数进行删除操作。

```
//删除一条管理员记录
function deleteAdmin(id) {

    $.post('admin/delete',{id:id}, function (result) {

        if(result != null && result.code == 0){
            showTips("操作成功");
            var table = $('#example2').DataTable();
            table.ajax.reload();
        }else{
            showTips(result.reason);
        }

    },'json');

}

1、删除逻辑，就是以当前选中的记录的id，访问后端删除管理员协议，完成删除操作。

2、删除完成之后，调用table.ajax.reload();刷新表格数据。

```

2.4 处理查询逻辑

上面可以看到，点击查询按钮的时候，我们直接调用了search函数。

```
//根据条件查询
function search() {
    var username = $("#searchName").val();
    var adminRoleName = $("#searchRoleName").val();

    if(isEmpty(username) && isEmpty(adminRoleName)){
        showTips("请输入查询条件");
        return;
    }

    var table = $('#example2').DataTable();
    table.ajax.url('admin/all?username='+username+'&adminRoleName='+adminRoleName);
    table.ajax.reload();
}

1、我们获取到查询控件中，用户输入的值。 然后判定有一个不为空，即可访问查询接口。

2、我们将初始化页面访问和查询协议合二为一了。

3、这里我们通过重新指定datatable的url，加上了两个查询参数，然后调用他的ajax.reload()方法，完成数据的重新请求和刷新。

4、这里不好用http post方式。 这里好像只能重新指定url，无法重新设置参数。

```

2.5 其他数据依赖

权限这里，我们是采用权限角色设计。也就是管理员关联一个权限角色(比如：超级管理员、测试人员、运维人员)。不同权限角色对应不同的功能权限。

管理员这里，在编辑信息界面，我们需要依赖管理员角色数据。可以指定管理员所属的权限角色。我们采用select控件，从服务器端获取所有权限角色数据，然后显示在select下拉列表控件中，让用户选择。

我们在点击新增或者编辑按钮的时候， 访问后端获取权限角色数据的接口，然后将返回的数据，设置到select控件中。

默认的select控件丑陋无比，这里使用了jquery的一个select插件：select2。

```
//请求管理员角色列表
function requestRoles(selectedID) {

    $.post('admin/roles',{}, function (result) {

        var itemList = [];
        if(result != null && result.code == 0){
            var lst = result.data;
            var itemIndex;
            for(itemIndex in lst){
                if(lst[itemIndex].id == selectedID){
                    itemList.unshift({id:lst[itemIndex].id, text:lst[itemIndex].roleName});
                }else{
                    itemList.push({id:lst[itemIndex].id, text:lst[itemIndex].roleName});
                }

            }
        }
        $("#adminRole").empty().trigger('change');
        $("#adminRole").select2({
            data:itemList
        });


    },'json');


}

1、通过http post请求，获取所有权限角色数据。

2、编辑的话，获取数据之后，需要默认选中当前管理员对应的权限角色。 这里我们让他处于第一个默认选中状态。

```

