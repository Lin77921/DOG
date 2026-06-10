各脚本功能简介
根目录脚本
脚本	功能
CubeController.cs	测试脚本，左键前进、空格旋转、右键放大
MouseManager.cs	鼠标管理器，根据射线检测的 Tag 切换光标，点击时发出地面/敌人事件
PlayerController.cs	玩家控制器，接收鼠标事件驱动 NavMeshAgent 移动和攻击
PlayerSkillManager.cs	技能释放，数字键1-9生成特效预制体，技能3附带飞弹飞行
Trigger.cs	按G键在固定坐标生成特效
Rock.cs	投石弹，可飞向玩家造成伤害，也可被玩家打向敌人
ToggleObjectByTab.cs	Tab键切换UI面板显隐
StopAgent.cs	动画状态机行为，特定动画期间锁住NavMeshAgent
Monster/脚本 目录
脚本	功能
Singleton.cs	泛型单例基类
GameManager.cs	游戏全局管理，注册玩家、管理相机、观察者模式通知游戏结束
IEndGameObserver.cs	游戏结束观察者接口
CharacterData_SO.cs	角色数据SO：血量、防御、等级、经验、升级逻辑
AttackData_SO.cs	攻击数据SO：伤害范围、冷却、暴击
ItemData_SO.cs	物品数据SO：类型、图标、武器预制体
InventoryData_SO.cs	背包数据SO：物品列表、堆叠添加
CharacterStats.cs	角色属性组件：读写血量防御、受伤计算、暴击、装备武器
EnemyController.cs	敌人AI：守卫→巡逻→追击→死亡 四状态机EnemyController.cs	敌人AI：守卫→巡逻→追击→死亡 四状态机
Golem.cs	石巨人，继承EnemyController，技能：踢飞+投石Golem.cs	石巨人，继承EnemyController，技能：踢飞 投石
Grunt.cs	小兵，继承EnemyController，技能：踢飞
ExtensionMethod.cs	扩展方法：判断是否面向目标
HealthBarUI.cs	头顶血条，世界空间跟随角色
PlayerHealthUI.cs	玩家HUD：等级+血条+经验条
InventoryManager.cs	背包管理器：三个容器（背包/快捷/装备）、拖拽范围检测
ContainerUI.cs	容器UI刷新
SlotHolder.cs	槽位，区分BAG/WEAPON/ARMOR/ACTION
ItemUI.cs	物品UI显示图标和数量
DragItem.cs	物品拖拽交换，支持堆叠合并
SceneController.cs	场景传送：同场景/跨场景切换、淡入淡出
SceneFader.cs	淡入淡出效果
TransitionPoint.cs	传送点，按E触发传送
TransitionDestination.cs	传送目的地标记
SaveManager.cs	存档系统：PlayerPrefs+JSON存读，S保存/L读取/Esc回主页
MainMenu.cs	主菜单：新游戏/继续/退出
ItemPickUp.cs	拾取物品，触碰后入背包并装备
核心关联关系
Singleton（基类）
  ├── MouseManager ──事件──→ PlayerController ──攻击──→ CharacterStats（受伤计算）
  ├── GameManager  ←─注册── PlayerController
  │     └── 观察者列表 → EnemyController（EndNotify）
  │                    → SceneController（EndNotify→回主菜单）
  ├── InventoryManager ←─── DragItem / SlotHolder / ContainerUI / ItemUI
  ├── SaveManager    ←─── SceneController / MainMenu / PlayerController
  └── SceneController ←─── TransitionPoint（E键传送）

CharacterStats（属性组件，挂在玩家和敌人身上）
  ├── 读取 → CharacterData_SO（血量/等级）├── 读取 → CharacterData_SO（血量/等级）
  ├── 读取 → AttackData_SO（攻击数据）├── 读取 → AttackData_SO（攻击数据）
  ├── 装备 → ItemData_SO（武器）
  └── 事件 → HealthBarUI（更新血条）

EnemyController（敌人AI基类）
  ├── Golem（踢飞 + 投石 → Rock）
  └── Grunt（踢飞）

数据流向：
  鼠标点击 → MouseManager → PlayerController → CharacterStats.TakeDamage → HealthBarUI更新鼠标点击 → MouseManager → PlayerController → CharacterStats.TakeDamage → HealthBarUI更新
                                                    ↓ 击杀
                                              CharacterData_SO.UpdateExp → PlayerHealthUI显示CharacterData_SO.UpdateExp → PlayerHealthUI显示
