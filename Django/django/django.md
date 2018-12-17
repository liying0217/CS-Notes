# virtualenv介绍
## virtualenv优点

1. 使不同应用开发环境独立
2. 环境升级不影响其它使用，也不影响全局的Python环境
3. 它可以防止系统中出现包管理混乱和版本的冲突

----------------------------------------------------
# Django app
### users-用户管理
### course-课程管理
### organization-机构和教师管理
### operation-用户操作管理
----------------------------------------------------
# 数据库设计
## 目录

### django app设计
### user models.py
### course models.py
### organization models.py
### operation models.py
----------------------------------------------------
# users models.py
### 自定义userprofile覆盖默认user表
### EmailVerifyRecord-邮箱验证码
### PageBanner-轮播图
----------------------------------------------------
# courses models.py
### Course-课程基本信息
### Lesson-章节信息
### Video-视频
### CourseResource-课程资源
----------------------------------------------------
# organization models.py
### CourseOrg-课程机构基本信息
### Teacher-教师基本信息
### CityDict-城市信息
----------------------------------------------------
# operation models.py
### UserAsk-用户咨询
### CourseComments-用户评论
### UserFavorite-用户收藏
### UserMessage-用户消息
### UserCourse- 用户学习的课程
----------------------------------------------------
# 后台管理系统
## 特点
### 权限管理
### 少前端样式
### 快速开发
----------------------------------------------------
