---
layout: post
title: "使用Jquery+bootstrap无限级菜单树"
date: 2017-08-10 12:43:39 +0800
comments: true
categories:
tags: [bootstrap,jquery,数据库,数据]
keyword: 陈浩翔, 谙忆
description:
---


无限级菜单树的实现效果从数据库提取出来的原始数据前台代码需要的数据{
    "code": 100,
    "extend": {
        "children": [
            {
                "children": [
                    {
                        "children": [
<!-- more -->
----------

### 无限级菜单树的实现
项目地址：https://github.com/EnTaroAdunZ/MenuTree.git
####效果
![](http://othgjp7hs.bkt.clouddn.com/17-8-10/58304335.jpg)
#### 从数据库提取出来的原始数据
![](http://othgjp7hs.bkt.clouddn.com/17-8-10/42243867.jpg)
#### 前台代码需要的数据
![](http://othgjp7hs.bkt.clouddn.com/17-8-10/61033634.jpg)
```
{
    "code": 100,
    "extend": {
        "children": [
            {
                "children": [
                    {
                        "children": [
                            {
                                "children": [],
                                "id": "4",
                                "parentId": "2",
                                "text": "用户管理"
                            },
                            {
                                "children": [],
                                "id": "5",
                                "parentId": "2",
                                "text": "角色管理"
                            },
                            {
                                "children": [
                                    {
                                        "children": [],
                                        "id": "7",
                                        "parentId": "6",
                                        "text": "权限增加"
                                    },
                                    {
                                        "children": [],
                                        "id": "8",
                                        "parentId": "6",
                                        "text": "权限删除"
                                    }
                                ],
                                "id": "6",
                                "parentId": "2",
                                "text": "权限管理"
                            }
                        ],
                        "id": "2",
                        "parentId": "1",
                        "text": "权限系统"
                    },
                    {
                        "children": [
                            {
                                "children": [],
                                "id": "9",
                                "parentId": "3",
                                "text": "轮播图管理"
                            },
                            {
                                "children": [],
                                "id": "10",
                                "parentId": "3",
                                "text": "商品管理"
                            }
                        ],
                        "id": "3",
                        "parentId": "1",
                        "text": "内容管理"
                    }
                ],
                "id": "1",
                "parentId": "1",
                "text": "主菜单"
            }
        ]
    },
    "msg": "操作成功"
}
```
#### 后台转换过程
#####BuildTree类
```
public class BuildTree {
    public static <T> List<Tree<T>> build(List<Tree<T>> nodes) {

        if (nodes == null) {
            return null;
        }
        List<Tree<T>> topNodes = new ArrayList<>();

        for (Tree<T> children : nodes) {

            String pid = children.getParentId();
            if (pid.equals(children.getId() ) || "0".equals(pid)) {
                topNodes.add(children);
                continue;
            }
            for (Tree<T> parent : nodes) {
                String id = parent.getId();
                if (id != null && id.equals(pid)) {
                    parent.getChildren().add(children);
                }
            }
        }

        return topNodes;
    }


}
```
#####Tree

```
public class Tree<T> {
    /**
     * 节点ID
     */
    private String id;
    /**
     * 显示节点文本
     */
    private String text;
    /**
     * 节点的子节点
     */
    private List<Tree<T>> children = new ArrayList<Tree<T>>();

    /**
     * 父ID
     */
    private String parentId;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getText() {
        return text;
    }

    public void setText(String text) {
        this.text = text;
    }

    public List<Tree<T>> getChildren() {
        return children;
    }

    public void setChildren(List<Tree<T>> children) {
        this.children = children;
    }

    public String getParentId() {
        return parentId;
    }

    public void setParentId(String parentId) {
        this.parentId = parentId;
    }
}
```
#####MenuDto类
```
public class MenuDto {
    private Long id;
    private Long parent_id;
    private String menuNmae;

    public MenuDto(Long id, String menuNmae, Long parent_id) {
        this.id = id;
        this.parent_id = parent_id;
        this.menuNmae = menuNmae;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public Long getParent_id() {
        return parent_id;
    }

    public void setParent_id(Long parent_id) {
        this.parent_id = parent_id;
    }

    public String getMenuNmae() {
        return menuNmae;
    }

    public void setMenuNmae(String menuNmae) {
        this.menuNmae = menuNmae;
    }
}
```
#####controller中的代码
```
 public Msg initMenu(){
        ModelAndView modelAndView=new ModelAndView("/main/index");
        List<Tree<MenuDto>> trees = new ArrayList<Tree<MenuDto>>();
        List<MenuDto> menuDtoList = new ArrayList<MenuDto>();
        menuDtoList.add(new MenuDto(1L,"主菜单",1L));
        menuDtoList.add(new MenuDto(2L,"权限系统",1L));
        menuDtoList.add(new MenuDto(3L,"内容管理",1L));
        menuDtoList.add(new MenuDto(4L,"用户管理",2L));
        menuDtoList.add(new MenuDto(5L,"角色管理",2L));
        menuDtoList.add(new MenuDto(6L,"权限管理",2L));
        menuDtoList.add(new MenuDto(7L,"权限增加",6L));
        menuDtoList.add(new MenuDto(8L,"权限删除",6L));
        menuDtoList.add(new MenuDto(9L,"轮播图管理",3L));
        menuDtoList.add(new MenuDto(10L,"商品管理",3L));

        for (MenuDto test : menuDtoList) {
            Tree<MenuDto> tree = new Tree<MenuDto>();
            tree.setId(test.getId().toString());
            tree.setParentId(test.getParent_id().toString());
            tree.setText(test.getMenuNmae());
            trees.add(tree);
        }
        List<Tree<MenuDto>> children=BuildTree.build(trees);
        return Msg.success().add("children",children);
    }
```
###### Jquery代码
```
$(document).ready(function () {
    var isFirstMenu;

    var menulist ={"code":100,"msg":"操作成功","extend":{"children":[{"id":"1","text":"主菜单","children":[{"id":"2","text":"权限系统","children":[{"id":"4","text":"用户管理","children":[],"parentId":"2"},{"id":"5","text":"角色管理","children":[],"parentId":"2"},{"id":"6","text":"权限管理","children":[{"id":"7","text":"权限增加","children":[],"parentId":"6"},{"id":"8","text":"权限删除","children":[],"parentId":"6"}],"parentId":"2"}],"parentId":"1"},{"id":"3","text":"内容管理","children":[{"id":"9","text":"轮播图管理","children":[],"parentId":"3"},{"id":"10","text":"商品管理","children":[],"parentId":"3"}],"parentId":"1"}],"parentId":"1"}]}};
    $(window).load(function(){
        var showlist = $("<ul class=\"sidebar-menu\"></ul>");
        $("<li class=\"header\">主导航</li>").appendTo(showlist);
        isFirstMenu=menulist.extend.children.length;
        showall(menulist.extend.children, showlist);
        $("#div_menu").append(showlist);
    });
     function showall(menu_list, parent) {
        for (var menu in menu_list) {
            if (menu_list[menu].children.length > 0) {
                var li = $("<li></li>");
                if(isFirstMenu==0){
                    li = $("<li></li>");
                }else{
                    li = $("<li class=\"treeview\"></li>");
                    isFirstMenu=isFirstMenu-1;
                }
                $(li).append("<a href=\"#\"><i class=\"fa fa-share\"></i> <span>"+menu_list[menu].text+"</span><i class=\"fa fa-angle-right pull-right\"></i></a>");
                var nextParent=$("<ul class=\"treeview-menu\"></ul>");
                $(nextParent).appendTo(li);
                $(li).appendTo(parent);
                showall(menu_list[menu].children, nextParent);
            }
            else {
                $("<li><a href=\"#\"><i class=\"fa fa-circle-o\"></i>"
                    +menu_list[menu].text
                    +"</a></li>").appendTo(parent);
            }
        }
    }

});
```
