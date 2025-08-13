### 乒乓球教学系统：核心工作流问题树 (完整版)

**`1. 系统的核心工作流是什么？`**

*   **`1.1. 平台初始化与全局管理工作流 (System-Level)`**
    *   `1.1.1. 系统如何从“零”到“一”？(初始化流程)`
        *   第一个注册用户如何成为 `system->sys_admin`？
        *   `system->developer` 角色是如何预设或在紧急情况下授予的？
        *   平台的全局设置（如品牌Logo、通知模板）由谁管理？(`system->can_manage_global_settings`)
    *   `1.1.2. 平台级用户管理工作流`
        *   `sys_admin` 如何邀请新的 `sys_admin`, `coach`, `student` 加入平台？(`system->can_invite_user`)
        *   `sys_admin` 如何管理平台上的所有用户（禁用、启用、重置密码、修改全局角色）？(`system->can_manage_users`)
        *   如何确保所有操作都有记录可查？(`system->can_access_audit_logs`)
    *   `1.1.3. 平台级组织管理工作流`
        *   `sys_admin` 如何创建顶级的 `class`（班级）？(`system->can_create_class`)
        *   未来如果引入“校区”或“俱乐部”的概念（作为 `class` 的父级），这个工作流如何扩展？

*   **`1.2. 班级（部门）生命周期与内部管理工作流 (Class-Level)`**
    *   `1.2.1. 班级是如何创建和配置的？`
        *   班级创建后，创建者如何自动成为 `class->owner`？
        *   `class->owner` 或 `class->coach` 如何修改班级的基础信息？(`class->can_manage_class_settings`)
    *   `1.2.2. 班级成员是如何管理的？(委托管理)`
        *   `class->owner` 或 `class->coach` 如何邀请或直接添加成员到班级？(`class->can_add_member`)
        *   `class->owner` 或 `class->coach` 如何将班级内的某个 `user` 指派为 `class->assistant_coach`？(`class->can_assign_assistant_coach`)
        *   如果主教练变更，`class->owner` 或 `system->sys_admin` 如何执行此高权限操作？(`class->can_change_coach`)
        *   成员如何从班级中被移除？(`class->can_remove_member`)
    *   `1.2.3. 班级数据是如何被使用的？`
        *   班级内的所有成员 (`class->member`) 如何查看班级的整体仪表盘？(`class->can_view_class_dashboard`)
        *   `class->owner` 或 `class->coach` 如何导出班级数据用于线下分析？(`class->can_export_class_data`)

*   **`1.3. 核心教学内容生命周期工作流 (Content Lifecycle)`**
    *   `1.3.1. 教学视频 (Teaching Video) 的生命周期是怎样的？`
        *   **创建**: `coach` (`teaching_video->author`) 如何上传视频草稿？(`teaching_video->can_upload`)
        *   **编辑**: `author` 如何编辑草稿的元数据或重新上传视频文件？(`teaching_video->can_edit_metadata`, `teaching_video->can_delete_draft`)
        *   **审核与发布 (自审核)**: `author` 如何进行自我审核并最终发布视频？(`teaching_video->can_review`, `teaching_video->can_publish`)
        *   **消费**: 视频发布后，哪些人（如 `system->platform_student` 或 `class->student`）可以看到？(`teaching_video->can_view`)
        *   **维护**: `author` 或 `sys_admin` 如何在发布后更新或删除视频？(`teaching_video->can_delete`)
    *   `1.3.2. 学生练习视频 (Student Practice Video) 的生命周期是怎样的？(多级审核)`
        *   **创建/上传**: `assistant_coach` (`student_practice_video->recorder`) 如何为某个 `student` 录制并上传练习视频？(`student_practice_video->can_upload`)
        *   **提交审核**: `recorder` 如何将上传的草稿视频提交给指定的 `reviewer` (通常是主教练)？(`student_practice_video->can_submit_for_review`)
        *   **审核**: `reviewer` 或 `sys_admin` 如何审批或驳回视频？(`student_practice_video->can_review`)
        *   **发布**: 审核通过后，`reviewer` 如何发布视频，使其对班级内成员可见？(`student_practice_video->can_publish`)
        *   **消费**: 发布后，谁能看到这个视频？(`student_practice_video->can_view`)
        *   **维护**: 谁有权删除已发布的视频？(`student_practice_video->can_delete`)
    *   `1.3.3. 学生学习报告 (Student Learning Report) 的生命周期是怎样的？(数据驱动与审核)`
        *   **数据录入**: `assistant_coach` (`student_learning_report->data_recorder`) 如何录入学生的练习数据？(`student_learning_report->can_record_data`)
        *   **生成与提交**: 系统如何基于数据自动生成报告草稿？`data_recorder` 如何提交报告进行审核？(`student_learning_report->can_submit_for_review`)
        *   **审核与发布**: 流程与练习视频类似，由 `reviewer` 进行审核 (`can_review`) 和发布 (`can_publish`)。
        *   **消费与维护**: 流程与练习视频类似。

*   **`1.4. 互动与反馈工作流 (Interaction & Feedback)`**
    *   `1.4.1. 评论是如何产生的？`
        *   任何有权查看教学资产 (`student_practice_video`, `teaching_video` 等) 的用户，如何在其下方发表评论？(`comment->can_create`)
    *   `1.4.2. 评论是如何被管理的？`
        *   评论的作者 (`comment->author`) 如何编辑或删除自己的评论？(`comment->can_edit`, `comment->can_delete`)
        *   资产的管理者（如 `reviewer`）或 `sys_admin` 如何删除不当评论？(`comment->can_delete`)

*   **`1.5. 个人空间与设置工作流 (Personal Space)`**
    *   `1.5.1. 用户如何管理自己的信息？`
        *   用户 (`user_profile->owner`) 如何查看和编辑自己的个人资料（如头像、昵称）？(`user_profile->can_view`, `user_profile->can_edit`)
        *   `sys_admin` 在何种情况下需要介入修改用户资料？
    *   `1.5.2. 用户的个人内容空间是怎样的？`
        *   学生是否有一个个人空间，可以看到所有与自己相关的视频和报告？
        *   教练是否有一个工作台，可以快速访问他所负责的所有班级和学生？

---

### 总结

这份完整的问题树现在覆盖了从平台宏观管理到个人微观操作的各个层面。它不仅定义了“做什么”，还通过引用 FGA 模型，清晰地指出了“谁能做”以及“如何实现”。

**这份文档的核心价值在于**:

1.  **全面性**: 几乎涵盖了所有可预见的业务流程，减少了开发过程中的需求遗漏。
2.  **结构化**: 树状结构使得系统逻辑清晰，便于团队分工和并行开发。
3.  **权限驱动**: 每个工作流都与具体的权限点紧密相连，确保了系统设计的安全性和一致性。
4.  **可操作性**: 为你的 Node.js 全栈开发提供了清晰的路线图。你可以根据这棵树来设计你的 API 路由、数据库 Schema 以及前端的用户界面和交互逻辑。