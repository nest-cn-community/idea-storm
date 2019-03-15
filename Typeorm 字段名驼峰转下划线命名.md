> 官网上面没有直接提示和说明如何驼峰转下划线，就算是查询API，和下载demo 里面都没有详细提供参考，特此根据使用经验，开辟此文章来完成对此方式详细说明。

### 1、Column
```typescript
    @Column()
    abc_abc;

    @Column({name: 'abc_abc'})
    abcAbc;
```

>直接写下划线命名即可在数据库中显示为下划线名称，如果讲究十分规范的话，请使用下面的写发，指定name为下划线名称。

### 2、OneToOne && ManyToOne
```typescript
    @OneToOne(……)
    @JoinColumn({ name: 'abc_abc'})
    foriegnObj: ForiegnObj;

    @ManyToOne(……)
    @JoinColumn({ name: 'abc_abc'})
    foriegnObj: ForiegnObj;
```
> OneToOne和 ManyToOne在同样的条件下会指定对应的外键，直接通过JoinColumn的name属性来控制外键名称。

### 3、ManyToMany
```typescript
    @ManyToMany(......)
    @JoinTable({name:'tableName' 
                        , joincolumn:{
                                    name:'abc_abc'
                                    referencedColumnName:'thistable_id'
                               }
                        , inverseJoinColumn:{
                                    name:'def_def'
                                    referencedColumnName:'pointtable_id'
                               }})

    foriegnObjs: ForiegnObj[];
```

> **多对多属性进行字段编辑的时候一定要注意以下几点：**
      1、joincolumn 是本表内部成员映射，其referencedColumnName 一定指向本表主键ID。
      2、inverseJoinColumn 是ManyToMany 映射对象表的属性，这里一定指向其表主键ID。
      3、两表主键ID一定要类型、长度保持一致，不然会报 "**ERROR 1215 (HY000): Cannot add foreign key constraint**" 错误。


---
*provider by stormKid*
*原文链接：https://www.jianshu.com/p/c8d3ba63e03c*
