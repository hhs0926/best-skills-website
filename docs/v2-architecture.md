# BestSkills v2.0 — Skills 社交生态平台 架构方案

> 目标：今天完成第二阶段 MVP，开始验证市场

## 一、技术栈

| 层级 | 技术选型 | 说明 |
|------|---------|------|
| **后端框架** | Laravel 11 + Livewire 3 | 全栈开发，前后端一体化 |
| **前端 UI** | FluxUI (Tailwind CSS) | 现代化组件库，与 Livewire 完美配合 |
| **数据库** | SQLite → MySQL | 开发用 SQLite（零配置），上线换 MySQL |
| **认证** | Laravel Breeze + GitHub OAuth | 用户注册/登录 |
| **部署** | VPS / 云服务器（后续） | 本地开发 → 部署上线 |
| **搜索** | 数据库全文搜索（初期）→ Meilisearch（后期） |

## 二、核心功能模块

### 模块1：用户系统
- [x] 邮箱注册/登录
- [x] GitHub OAuth 一键登录
- [x] 用户个人主页（头像、简介、我的Skills、我的帖子）
- [ ] 关注/粉丝系统

### 模块2：Skills 展示与评测（从v1升级）
- [x] Skill 详情页（安装步骤/配置代码/适用场景/优缺点）
- [x] 用户评分（5星+文字评价）
- [x] 点赞/收藏
- [x] 使用量统计展示
- [ ] 提交新 Skill 入口

### 模块3：社区互动
- [x] 帖子发布（图文）
- [x] 评论系统（支持嵌套回复）
- [x] 点赞/分享
- [ ] 话题标签 (#skills推荐 #agent开发)

### 模块4：技能交换市场 ⭐ 核心差异化
- [ ] "我会 X，想学 Y" 发布卡片
- [ ] AI 匹配推荐（基于技能标签）
- [ ] 交换请求/私信沟通
- [ ] 成功交换记录墙

### 模块5：资源共享
- [ ] 配置文件/模板上传下载
- [ ] 使用教程分享
- [ ] 推荐组合（Skill包）

## 三、数据库设计

```
users (用户表)
├── id, name, email, password
├── github_id, avatar_url, bio
├── created_at

skills (技能表)
├── id, name, slug, description, icon
├── category, install_count, rating_avg
├── install_steps (JSON), config_code (JSON)
├── pros_cons (JSON), use_cases (JSON)
├── created_by (FK → users.id)

skill_reviews (技能评价表)
├── id, skill_id (FK), user_id (FK)
├── rating (1-5), comment
├── created_at

posts (帖子表)
├── id, user_id (FK), title, content
├── type: 'post' | 'exchange' | 'resource'
├── tags (JSON), views, likes_count
├── created_at

comments (评论表)
├── id, post_id (FK), user_id (FK)
├── parent_id (FK → self, 支持嵌套回复)
├── content, likes_count
├── created_at

likes (点赞表 - 多态关联)
├── id, user_id ( FK)
├── likeable_type ('skill'|'post'|'comment')
├── likeable_id
├── created_at

bookmarks (收藏表)
├── id, user_id (FK), skill_id (FK)
├── created_at

exchange_posts (技能交换表)
├── id, user_id (FK)
├── have_skills (JSON)    # 我会什么
├── want_skills (JSON)    # 我想学什么
├── status: 'open' | 'matched' | 'closed'
├── created_at

follows (关注关系表)
├── id, follower_id (FK), following_id (FK)
├── created_at
```

## 四、页面路由设计

```
/                    → 首页（热门Skills + 最新动态）
/skills              → Skills 列表（分类筛选/搜索）
/skills/{slug}       → Skill 详情页 + 评价区
/skills/submit       → 提交新 Skill（需登录）
/exchange            → 技能交换市场
/posts               → 社区动态（信息流）
/posts/{id}          → 帖子详情 + 评论区
/user/{username}     → 个人主页
/login               → 登录/注册
/settings            → 设置
/search?q=xxx        → 全局搜索
```

## 五、开发计划（今日执行）

### Phase A: 项目初始化（预计30分钟）
1. 创建 Laravel 项目
2. 安装 Livewire + FluxUI + Breeze
3. 配置数据库和迁移文件
4. 导入现有12个Skill数据

### Phase B: 用户系统（预计45分钟）
1. Breeze 认证脚手架
2. GitHub OAuth 集成
3. 用户个人主页

### Phase C: Skills 升级（预计60分钟）
1. 从静态 HTML 迁移到 Livewire 组件
2. 评分/评价功能
3. 点赞/收藏功能
4. 提交新 Skill 入口

### Phase D: 社区互动（预计60分钟）
1. 发帖功能（Livewire）
2. 评论系统（嵌套回复）
3. 信息流页面

### Phase E: 技能交换（预计45分钟）
1. "我会X想学Y" 发布卡片
2. 交换列表页 + 筛选匹配
3. 基础版匹配逻辑

### Phase F: 部署验证（预计30分钟）
1. 本地测试全部功能
2. 准备部署文档
3. 市场验证方案输出

**总计预估：~5小时**

## 六、市场验证方案

### 第一波：种子用户（上线第1周）
- 在即刻/V2EX/知乎发帖："做了一个 Claude Skills 精选社区"
- Reddit r/ClaudeAI / r/codewithclaude 发帖
- Twitter/X 发 Thread
- 目标：50个注册用户，20条真实评价

### 第二波：数据驱动优化（第2-3周）
- 分析哪些 Skill 最受欢迎
- 收集用户最想要的功能
- 迭代技能交换匹配算法

### 第三波：增长飞轮（第4周+）
- UGC 内容激励（活跃用户获得"Skills专家"徽章）
- 引入付费 Skill 推荐/置顶（变现探索）
- API 开放给第三方
