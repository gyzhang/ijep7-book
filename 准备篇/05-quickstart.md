# 第5章 快速开始

在上一章中，我们介绍了安装部署 iJEP 7 的详细步骤。

这一章，我们首先以无代码的形式（不用手写一行代码，虽然当前代码生成器还存在缺陷需要手写骨架代码，后续会修正这个缺陷）开发配置一个 CRUD 的列表表单功能。然后，设计一个两个节点的工作流模板，并配置流程列表智能表单完成工作流审批功能。

以上场景为 iJEP 7 最常用的业务场景，我们的 Quick Start 就以此为开始。

> 开发配置过程中使用的用户为 superAdmin，密码是 123456

## 5.1 创建数据库表

使用如下脚本创建 MySQL 数据库表 demo_person：

```sql
CREATE TABLE `demo_person` (
  `id_` varchar(64) COLLATE utf8_bin NOT NULL COMMENT '主键',
  `code_` varchar(32) COLLATE utf8_bin DEFAULT NULL COMMENT '编码',
  `name_` varchar(64) COLLATE utf8_bin DEFAULT NULL COMMENT '姓名',
  `age_` int(11) DEFAULT NULL COMMENT '年龄',
  `mail_` varchar(128) COLLATE utf8_bin DEFAULT NULL COMMENT '邮箱',
  `WF_ID_` varchar(32) CHARACTER SET utf8 DEFAULT NULL COMMENT '流程实例ID',
  `WF_STATUS_` varchar(32) CHARACTER SET utf8 DEFAULT NULL COMMENT '流程审批状态',
  `WF_TASK_ID_` varchar(32) CHARACTER SET utf8 DEFAULT NULL COMMENT '流程任务实例ID',
  `GROUP_CODE_` varchar(64) CHARACTER SET utf8 DEFAULT NULL COMMENT '租户',
  `CREATED_BY_ID_` varchar(64) CHARACTER SET utf8 DEFAULT NULL COMMENT '创建人ID',
  `CREATED_BY_NAME_` varchar(64) CHARACTER SET utf8 DEFAULT NULL COMMENT '创建人名称',
  `CREATED_TIME_` datetime DEFAULT NULL COMMENT '创建时间',
  `MODIFIED_BY_ID_` varchar(64) CHARACTER SET utf8 DEFAULT NULL COMMENT '修改人ID',
  `MODIFIED_BY_NAME_` varchar(64) CHARACTER SET utf8 DEFAULT NULL COMMENT '修改人名称',
  `MODIFIED_TIME_` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  `OWNER_CODE_` varchar(64) CHARACTER SET utf8 DEFAULT NULL COMMENT '所有人编码',
  `OWNER_NAME_` varchar(128) CHARACTER SET utf8 DEFAULT NULL COMMENT '所有人名称',
  `OWNER_ORG_CODE_` varchar(64) CHARACTER SET utf8 DEFAULT NULL COMMENT '所属机构编码',
  `OWNER_ORG_NAME_` varchar(128) CHARACTER SET utf8 DEFAULT NULL COMMENT '所属机构名称',
  `OWNER_DEPT_CODE_` varchar(64) CHARACTER SET utf8 DEFAULT NULL COMMENT '所属部门编码',
  `OWNER_DEPT_NAME_` varchar(128) CHARACTER SET utf8 DEFAULT NULL COMMENT '所属部门名称',
  `DELFLAG_` varchar(1) CHARACTER SET utf8 DEFAULT NULL COMMENT '逻辑删除标记',
  `REV_` int(11) DEFAULT NULL COMMENT '版本',
  PRIMARY KEY (`id_`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='演示人员表';
```

## 5.2 生成骨架代码

访问**”系统管理 > 平台工具 > 代码生成器“**菜单，打开 iJEP 7 平台提供的代码生成器，设置“数据库配置”信息，测试后保存，连接到开发数据库：

![image-20211214211350235](images/image-20211214211350235.png)

在表名输入框中输入 demo% 单击**查询**按钮，然后选中 demo_person 表这一行，单击**生成**按钮：

![image-20211214215731210](images/image-20211214215731210.png)

输入包名 com.pactera.jep.service，服务名 customer，然后单击**完成**按钮：

![image-20211214211852645](images/image-20211214211852645.png)

稍等，可在下载目录中找到“iJEP_20211214211935.zip”文件，这个文件就是生成的骨架代码文件。

> 当前代码生成器生成的代码（7 个文件），针对工作流场景的骨架代码有 3 个缺陷，见后续步骤说明。

## 5.3 在 IDEA 中调整骨架代码

将生成的骨架代码文件拷贝到 IDEA 中，并做简单调整（后续代码生成器修正后就无需调整）。

> <font color = "red">当前版本代码生成模板还有点儿问题，请手动删除实体 DemoPerson.java 中的 wfTaskId 属性和对应的 getter、setter 方法，原因是基类 BpmPO 中有这个属性。</font>

![image-20211214215323844](images/image-20211214215323844.png)

手工创建流程服务类接口 **DemoPersonBpmApplicationService**：

```java
package com.pactera.jep.service.customer.service;

import com.pactera.jep.service.customer.model.DemoPerson;
import com.pactera.jep.web.service.BpmApplicationService;

public interface DemoPersonBpmApplicationService extends BpmApplicationService<DemoPerson, String> {

    String beanId = "demoPersonBpmApplicationService";
}
```

手工创建流程服务实现类 **DemoPersonBpmApplicationServiceImpl**：

```java
package com.pactera.jep.service.customer.service.impl;

import com.pactera.jep.core.exception.ServiceException;
import com.pactera.jep.orm.service.CRUDService;
import com.pactera.jep.service.customer.model.DemoPerson;
import com.pactera.jep.service.customer.service.DemoPersonBpmApplicationService;
import com.pactera.jep.service.customer.service.DemoPersonService;
import com.pactera.jep.web.service.impl.AbstractBpmApplicationService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service(DemoPersonBpmApplicationService.beanId)
@Transactional(rollbackFor = ServiceException.class)
public class DemoPersonBpmApplicationServiceImpl extends AbstractBpmApplicationService<DemoPerson, String> implements DemoPersonBpmApplicationService {

    @Autowired
    private DemoPersonService demoPersonService;


    @Override
    public CRUDService<DemoPerson, String> getCrudService() {
        return demoPersonService;
    }
}
```

> <font color = "red">当前版本代码生成器缺失工作流服务接口和实现类模板，请手动创建上述工作流服务接口和实现类。</font>

启动开发示例服务 customer：

![image-20211214213951581](images/image-20211214213951581.png)

## 5.4 配置智能表单

访问**“智能表单管理 > 业务模型”**菜单，检查是否自动扫描到了 demoPerson 实体模型：

![image-20211214215554705](images/image-20211214215554705.png)

访问**“智能表单管理 > 智能表单”**，选中“开发培训”分类，单击**新增**按钮：

![image-20211214214802974](images/image-20211214214802974.png)

### 5.4.1 编辑表单

选择**“仅编辑”**表单设计模板，输入“编码、名称”信息，选择“业务模型、业务实现”后单击**保存**按钮：

![image-20211214220033709](images/image-20211214220033709.png)

单击**“设计页”** tab，设计编辑表单：

![image-20211214220327717](images/image-20211214220327717.png)

添加编辑字段并设置其属性，然后**保存**：

![image-20211214220731422](images/image-20211214220731422.png)

选择**”权限管理“** tab，为”总行管理岗“设置本编辑表单的组织权限（数据权限）和字段权限：

![image-20211214223853710](images/image-20211214223853710.png)

然后启用本表单。

### 5.4.2 显示表单

将编辑表单复制为仅显示的表单：

![image-20211214220957787](images/image-20211214220957787.png)

输入“编码”和“名称”，然后单击**保存**按钮，生成显示表单：

![image-20211214221125603](images/image-20211214221125603.png)

选择**”权限管理“** tab，为”总行管理岗“设置本显示表单的组织权限（数据权限）和字段权限：

![image-20211214224145861](images/image-20211214224145861.png)

然后启用本表单。

### 5.4.3 列表表单

新增智能表单，选择**“仅列表”**表单设计模板，输入“编码、名称”信息，选择“业务模型、业务实现”后单击**保存**按钮：

![image-20211214221534619](images/image-20211214221534619.png)

添加查询条件，设置表格列属性，然后单击**保存**按钮，完成列表表单设计：

![image-20211214221930060](images/image-20211214221930060.png)

为列表表单上的按钮绑定对应的智能表单：

![image-20211214224727582](images/image-20211214224727582.png)

选择”权限管理“ tab，为”总行管理岗“设置本列表表单的组织权限（数据权限）、字段权限和功能权限：

![image-20211214223519236](images/image-20211214223519236.png)

然后启用本表单。

### 5.4.4 流程列表

在完成流程模板配置并启用该流程后，才可配置流程列表。

通常的业务场景是在一个列表界面提交审批来启动一个流程实例。

新建一个智能表单，选择**“流程列表”**模板，并填写相关属性值，单击**保存**按钮：

![image-20211215001337905](images/image-20211215001337905.png)

在**“设计页”** tab 添加查询条件，设置工具栏按钮，并为按钮指定智能表单，为表格添加列，然后单击**保存**按钮：

![image-20211215001732515](images/image-20211215001732515.png)

需要注意的是流程列表表单需要在表格的通用属性上设定**“业务种类”**和**“流程”**：

> 如果忘记设置“业务种类”和“流程”这两个属性，则在后续“提交”启动流程时会报<font color=red>btypeCode不能为空</font>错误。

![image-20211215004658685](images/image-20211215004658685.png)

选择**”权限管理“** tab，为”总行管理岗“设置本流程列表表单的组织权限（数据权限）、字段权限和功能权限：

![image-20211215002220177](images/image-20211215002220177.png)

然后启用本表单。

## 5.5 配置工作流

配置工作流，需要先配置业务类型，然后再配置流程模板。

### 5.5.1 新增业务类型

访问**“工作流管理 > 业务管理”**菜单，新增一个业务类型：

![image-20211214234607763](images/image-20211214234607763.png)

> 因为要选择智能表单，所以需要先配置 DemoPerson 实体的编辑/显示表单。

### 5.5.2 设计流程模板

访问**“工作流管理 > 流程管理”**菜单，新增一个流程模板，输入“编码、名称”信息，并选择“类型、智能表单和业务服务”属性，然后**保存**：

![image-20211214235228990](images/image-20211214235228990.png)

选中上一步新增的流程模板，单击**设计**按钮：

![image-20211214235406078](images/image-20211214235406078.png)

添加两个人工节点，设置其任务审批候选人等信息，为任务节点挂接智能表单：

> 简单起见，这里使用了指定审批人任务分配策略，第一步审批人为 lisi，最后一步审批人为 wangwu

![image-20211215000330675](images/image-20211215000330675.png)

保存后，选中该流程并测试该流程。

在流程测试界面，填写相关信息后，单击**流程测试**按钮，可自动的测试该流程是否可正确流转：

![image-20211215000524037](images/image-20211215000524037.png)

流程测试完成，可查看流程流转记录：

![image-20211215000657190](images/image-20211215000657190.png)

通过测试的流程，可在流程管理列表中启用该流程，供后续业务开发用：

![image-20211215001110943](images/image-20211215001110943.png)

## 5.6 配置菜单

访问**”系统管理 > 系统配置 > 菜单配置“**菜单，在”开发培训“菜单下的”员工请假报表“菜单上右键单击，选择”增加同级“菜单：

![image-20211214222524545](images/image-20211214222524545.png)

输入”菜单编码、菜单名称“等信息，选择对应的”演示人员列表“智能表单，然后单击**保存**按钮：

![image-20211214222841849](images/image-20211214222841849.png)

以同样的方法添加“演示人员管理-流程”菜单：

![image-20211215002623829](images/image-20211215002623829.png)

## 5.7 授权

访问**”系统管理 > 机构人员权限 > 授权管理“**菜单，为”总行管理岗“授权上面配置好的”演示人员管理“菜单权限：

![image-20211214223142486](images/image-20211214223142486.png)

为”30110 总行管理岗“授权”演示人员管理“菜单：

![image-20211214223313686](images/image-20211214223313686.png)

为”30110 总行管理岗“授权”演示人员管理-流程“菜单：

![image-20211215002813835](images/image-20211215002813835.png)

## 5.8 测试

完成上面的配置后，我们分两个场景来测试验证配置是否正确。

测试过程中会用到 zhangsan、lisi 和 wangwu 三个用户，他们的密码都是 123456。

### 5.8.1 测试”演示人员管理“功能

使用 ”zhangsan“ 用户（总行管理岗）登录系统后，访问**”开发培训 > 演示人员管理“**菜单，单击**新增**按钮，输入对应的字段值，注意邮箱输入框带校验功能：![image-20211214224955920](images/image-20211214224955920.png)

可在列表界面中查看到上面添加的”Kevin张“人员信息，同时可以观察到在列表表单中配置的功能权限（启用、停用、导入、导出按钮不可用）：

![image-20211214225200732](images/image-20211214225200732.png)

输入姓名”Kevin“可模糊查询到数据：

![image-20211214230059994](images/image-20211214230059994.png)

输入姓名”Kevin李“查找结果为空，验证测试条件功能生效：

![image-20211214230244868](images/image-20211214230244868.png)

单击**编辑**按钮，以新页面的形式展示编辑表单：

> 新增按钮设置为”弹窗“，和编辑的”新页面“显示效果不一样。

![image-20211214230431477](images/image-20211214230431477.png)

单击操作列中的**显示**按钮，展示显示表单：

![image-20211214230653027](images/image-20211214230653027.png)

### 5.8.2 测试”演示人员管理-流程“功能

使用 ”zhangsan“ 用户（总行管理岗）登录系统后，访问**”开发培训 > 演示人员管理-流程“**菜单，单击**新增**按钮，输入对应的字段值，注意邮箱输入框带校验功能：

![image-20211215003019696](images/image-20211215003019696.png)

在列表页面，选中上面新增的人员，并单击**提交**按钮，启动审批流程：

![image-20211215003055756](images/image-20211215003055756.png)

提交后，系统会提示“流程启动成功”：

> 如果“提交”启动流程时报<font color=red>btypeCode不能为空</font>错误，原因是没有为流程列表的表格设定“业务种类”和“流程”这两个属性，也就是说，这个流程列表智能表单的表格没有绑定到工作流模板。

![image-20211215005029836](images/image-20211215005029836.png)

选中“张大帅哥”这条记录，单击**查看审批流程**按钮，可查看当前流程的流转情况，可看到下一节点是由 lisi 审批：

![image-20211215005521005](images/image-20211215005521005.png)

使用 ”lisi“ 用户（总行管理岗）登录系统后，访问**”工作流管理 > 我的审批 > 待审批“**菜单，选中“演示人员审批工作流”这条记录，单击**处理**按钮：

![image-20211215005756185](images/image-20211215005756185.png)

填写处理意见后，单击**同意**按钮，完成审批：

![image-20211215010020173](images/image-20211215010020173.png)

换 ”zhangsan“ 用户（总行管理岗）登录系统后，访问**”开发培训 > 演示人员管理-流程“**菜单，选中“张大帅哥”这条记录，单击**查看审批流程**按钮，可查看当前流程李四已经审批，下一步由王五审批：

![image-20211215010137278](images/image-20211215010137278.png)

## 5.9 小结

本章，我们详细介绍了如何在 iJEP 7 中完成普通 CRUD 的功能开发，并进一步演示了在线设计工作流模板，然后配置流程列表智能表单，完成流程审批。

本章的 Quick Start 示例，不仅仅是低代码，而是无代码。