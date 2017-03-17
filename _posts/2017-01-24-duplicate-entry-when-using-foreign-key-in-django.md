---
title: 使用django时, duplicate entry的异常
layout: post
category: [tech]
tags: [django,database]
---
{% include JB/setup %}
#环境
+ MySQL
+ Django

# model:

    class AR(models.Model):
        emc_array = models.ForeignKey(EMC_Array, default=None, blank=True, null=True)
        player = models.ForeignKey(Player, default=None, blank=True, null=True)

    class EMC_Array(models.Model):
        player = models.ForeignKey(Player, default=None, blank=True, null=True)
        name = models.CharField(unique=True, max_length=16)
    
    class Player(models.Model):
        name = models.CharField(unique=True, max_length=100)
    
# 解决方法
首先inspect db, 发现实际定义与model定义有差异:

    h@ubuntu1604:~/tai$ python manage.py inspectdb
    ...
    class TeamAr(models.Model):
        emc_array = models.ForeignKey('TeamEmcArray', unique=True, blank=True, null= True)
        player = models.ForeignKey('TeamPlayer', blank=True, null=True)
    class TeamPlayer(models.Model):
        name = models.CharField(unique=True, max_length=100)
    class TeamEmcArray(models.Model):
        name = models.CharField(unique=True, max_length=16)
    
用 mysql直接登录后, 发现:

    mysql> show create table team_ar;
    ...
    ...
      `emc_array_id` int(11) DEFAULT NULL,
      `player_id` int(11) DEFAULT NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `emc_array_id` (`emc_array_id`),
      KEY `team_ar_afe72417` (`player_id`),
      CONSTRAINT `team_ar_emc_array_id_3409de4974ada960_fk_team_emc_array_id` FOREIGN KEY (`emc_array_id`) REFERENCES `team_emc_array` (`id`),
      CONSTRAINT `team_ar_player_id_25ea0e40d24ed0b1_fk_team_player_id` FOREIGN KEY (`player_id`) REFERENCES `team_player` (`id`),
    ) ENGINE=InnoDB AUTO_INCREMENT=320 DEFAULT CHARSET=utf8 |

此时,需要drop约束:

    mysql> alter table team_ar drop foreign key team_ar_emc_array_id_3409de4974ada960_fk_team_emc_array_id;

变成:

    `emc_array_id` int(11) DEFAULT NULL,
    `player_id` int(11) DEFAULT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY `emc_array_id` (`emc_array_id`),
    KEY `team_ar_afe72417` (`player_id`),
    CONSTRAINT `team_ar_player_id_25ea0e40d24ed0b1_fk_team_player_id` FOREIGN KEY (`player_id`) REFERENCES `team_player` (`id`),
    CONSTRAINT `team_ar_team_id_53952cd821c31c0f_fk_team_team_id` FOREIGN KEY (`team_id`) REFERENCES `team_team` (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=320 DEFAULT CHARSET=utf8 |

再删除index:    

    mysql> alter table team_ar drop index emc_array_id ;

变成:
    ...
    `emc_array_id` int(11) DEFAULT NULL,
    `player_id` int(11) DEFAULT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY `Issue_id` (`Issue_id`),
    KEY `team_ar_afe72417` (`player_id`),
    CONSTRAINT `team_ar_player_id_25ea0e40d24ed0b1_fk_team_player_id` FOREIGN KEY (`player_id`) REFERENCES `team_player` (`id`),
    ) ENGINE=InnoDB AUTO_INCREMENT=320 DEFAULT CHARSET=utf8 | ...

