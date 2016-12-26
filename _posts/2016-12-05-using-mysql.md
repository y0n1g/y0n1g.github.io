---
title: 使用mysql
layout: post
category: [tech]
tags: [mysql]
---
{% include JB/setup %}
### 创建一个由两列组成主键(复合键)的表

    CREATE  TABLE `test`.`new_table` (
      `person_id` INT NOT NULL ,
      `class_id` INT NOT NULL ,
      `score` INT NULL ,
      PRIMARY KEY (`person_id`, `class_id`) );

有人认为不应该使用复合键.
