##Survival Shooter
###01. Enviroment Setup
    
    >>scenes: level 01 (2 by 3)
    >>Assets/Prefabs/Environment─┬─>Hierarchy;
                                 └─Position: X0, Y0, Z0;
    >>GameObject─┬─3D Oject─┬─Quad─┬─Position: X0, Y0, Z0;
                 │          │      ├─Rotation: X90, Y0, Z0;
                 │          │      ├─Scale: x100, Y100, Z1;
                 │          │      └─Rename Quad to Floor;
                 │          └─Floor─┬─Mesh Renderer───Remove Component;
                 │                  └─Inspector/Layer:Floor;
                 ├─Create Empty───Rename: BackgroundMusic;
                 └─BackgroundMusic─┬─Add Component: Audio/Audio Source;
                                   ├─AudioClip: Background Music;
                                   ├─Play on Awake: ×;
                                   └─Loop: √;
 
###02. Player Charater

    >>Assets/Models/Character/Player──>Hierarchy;
    >>Player─┬─Position: X0, Y0, Z0;
             └─Tag: from Untagged to Player;
    >>Assets/Animation─┬─Create: Animator Controller───Name: Player AC;
                       └─Player AC──>Player;
    >>Animator─┬─Character/Player/Death,Idle,Move──>Animator;
               ├─Idle───Set as Layer Default State;
               ├─Parameters─┬─Create: Bool: IsWalking;
               │            └─Create: Trigger: Die;
               └─Make a Transition─┬─(Idle->Move): Conditions: IsWalking: true;
                                   └─(Move->Idle): Conditions: IsWalking: false;
    >>Player─┬─Add Component: Physics/Rigidbody─┬─Drag: Infinity;
             │                                  ├─Angular Drag: Infinity;
             │                                  └─Constraints─┬─Freeze Position: √Y;
             │                                                └─Freeze Rotation: √X, √Z;
             ├─Add Component: Physics/Capsule Collider─┬─Center: X0.2, Y0.6, Z0;
             │                                         └─Height: 1.2;
             └─Add Component: Audio/Audio Source─┬─AudioClip: Player Hurt;
                                                 └─Play on Awake: ×;
    >>Scripts/Player/PlayerMovement──>Hierarchy/Player;

###03. Camera Setup

    >>Main Camera─┬─Position: X1, Y15, Z-22;
                  ├─Rotation: X30, Y0, Z0;
                  ├─Projection: Orthographic;
                  ├─Size: 4.5;
                  └─Background: Black;
    >>Scripts/Camera─┬─Create: C# script───Name: CameraFollow;
                     └─CameraFollow──>Main Camera;
    >>Hierarchy/Player──>Main Camera/Target;
    >>Hierarchy/Player──>Assets/Prefabs;

###04. Creating Enemy #1

    >>Assets/Models/Character/Zombunny──># Scene;
    >>Assets/Prefabs/HitParticles──>Hierarchy/Zombunny(parent);
    >>Hierarchy/Zombunny─┬─Layer: Shootble───Change the children.
                         ├─Add Component: Rigidbody─┬─Drag: Infinity;
                         │                          ├─Angular Drag: infinity;
                         │                          ├─Freeze Position: √Y;
                         │                          └─Freeze Rotation: √X, √Z;
                         ├─Add Component: Capsule Collider─┬─Center: X0, Y0.8, Z0;
                         │                                 └─Height: 1.5;
                         ├─Add Component: Sphere Collider─┬─Is Trigger: √;
                         │                                ├─Center: X0, Y0.8, Z0;
                         │                                └─Radius: 0.8;
                         └─Add Component: Audio Source─┬─AudioClip: ZomBunny Hurt;
                                                       └─Play on Awake: ×;
    >>Window/Navagation─┬─Zombunny(parent)───Add Component: Nav Mesh Agent;
                        └─Nav Mesh Agent─┬─Radius: 0.3;
                                         ├─Speed: 3;
                                         ├─Stopping Distance: 1.3;
                                         └─Height: 1.1;
    >>Environment, Navigation/Bake─┬─Radius: 0.75;
                                   ├─Height: 1.2;
                                   ├─Step Height: 0.1;
                                   ├─(Advanced/Width Inaccuracy: 1%);
                                   └─"Bake" Button;
    >>Assets/Animation───Create: Animator Controller───Name: EnemyAC──>Hierarchy/Zomebunny/Animator:Controller;
    >>Assets/Models/Characters/Zombunny: Death,Idle,Move──>EnemyAC:Animator;
    >>Animator─┬─Move───Set as Layer Default State;
               ├─Parameters─┬─Create: Trigger: PlayerDead;
               │            └─Create: Trigger: Dead;
               └─Make a Transition─┬─(Move->Idle): Conditions: PlayerDead;
                                   └─(Any State->Death): Conditions: Dead;
    >>Assets/Scripts/Enemy:Enemy Movement──>Hierarchy/Zombunny;

###05. Health HUD

    >># Scene───2D;
    >>GameObject───UI───Canvas───Name: HUDCanvas;
    >>HUDCanvas─┬─Add Components: Canvas Group;
                ├─Canvas Group─┬─Interactable: ×;
                │              └─Blocks Raycasts: ×;
                ├─Create Empty───Name: HealthUI;
                ├─HealthUI/ReactTransform─┬─Shift: Also set pivot─┬─bottom&left;
                │                         ├─Alt: Also set position┘
                │                         ├─Width: 75, Height: 60;
                │                         └─right click───UI/Image───Name: Heart;
                ├─Heart─┬─Width: 30, Height: 30;
                │       └─Image(Script)/Source Image───Heart;
                ├─HealthUI───right click───UI/Slider───Name: HealthSlider;
                └─HealthSlider─┬─Pos X: 95;
                               ├─delete Hierarchy/HealthSlider/Handle Slide Area;
                               ├─Transition: None;
                               ├─Max Value: 100;
                               └─Value: 100;
    >>HealthUI───right click───UI/image───Name: DamageImage──>HUDCanvas;
    >>DamageImage─┬─Anchor Presets───Alt: stretch&stretch;
                  └─Image(Script)───Color───(RGB)A: 0;

###06. Player Health

    >>Assets/Scripts/Player/Player Health──>Hierarchy/Player;
    >>HUDCanvas/HealthUI/HealthSlider──>Player/Player Health(Script)/Health Slider;
    >>HUDCanvas/DamgeImage──>Player/Player Health(Script): Damge Image;
    >>Player/Player Health(Script)───Death Clip: Player Death;
    >>Assets/Scripts/Enemy/EnemyAttack──>Hierarchy/Zombunny;

###07. Harming Enemies

    >>Assets/Scripts/Enemy/EnemyHealth──>Hierarchy/Zombunny;
    >>Hierarchy/Zombunny─┬─EnemyHealth(Script)───Death Clip:Zombunny Death;
                         └─Death/Events;
    >>Assets/Prefabs───GunParticles───Copy Component;
    >>Hierarchy/Player───GunBarrelEnd─┬─Paste Component As New;
                                      ├─Add Component: Line Renderer;
                                      ├─Line Renderer─┬─Materials───Line Renderer Material;
                                      │               ├─Parameters─┬─Start Width: 0.05;
                                      │               │            └─End Width: 0.05;
                                      │               └─X Line Renderer (invisible);
                                      ├─Add Component: Rendering/Light;
                                      ├─Light─┬─Color: yellow;
                                      │       └─X Light (invisible);
                                      ├─Add Component: Audio Source;
                                      └─Audio Source─┬─AudioClip───Player Gunshot;
                                                     └─Play On Awake: ×;
    >>Assets/Scripts/Player/Player Shooting──>Hierarchy/Player/GunBarrelEnd;
    >>Hierarchy/Player──>Apply;
    >>Console/error───playerHealth&enemyHealth───delete //;
    >>playerHealth─┬─void Death()─┬─playerShooting.DisableEffects─┬─delete //;
                   │              └─playerShooting.enabled────────┘
                   └─public class PlayerHealth: MonoBehaviour───playerShooting───delete //;

###08. Scoring Points

    >>HUDCanvas─┬─UI/Text───Name: ScoreText;
                └─ScoreText─┬─Anchor Presets: top&center;
                            ├─Pos Y: -55;
                            ├─Width:300, Height:50;
                            ├─Text(Script)─┬─Color: white;
                            │              ├─Character/Font: LukiestGuy;
                            │              ├─Character/Font Size: 50;
                            │              ├─Alignment: center;
                            │              └─Text: score: 0;
                            ├─Add Component: Shadow;
                            └─Shadow───Effect Distance: X2, Y-2;
    >>Scripts/Manager/ScoreManager──>HUDCanvas/ScoreText;
    >>EnemyHealth.cs──>pulibc void StartingSinking───delete //: ScoreManager.Score += scoreValue;
    >>Hierarchy/Zombunny──>Assets/Prefabs;
    >>Hierarchy/Zombunny───delete;

###09. Spawn Enemies

    >>Animation/EnemyAC──>Prefabs/Zombear/Animator/Controller;
    >>Animation───create Animator Override Controller───Name: HellephantAOC;
    >>Animation/EnemyAC──>Animation/Hellephant/Controller;
    >>Animation/HellephantAOC──>Prefabs/Hellephant/Animator/Controller;
    >>Hierarchy───create Empty───Name: EnemyManager;
    >>EnemyManager───Transform───Reset Position;
    >>Scripts/Manager/EnemyManager──>Hierarchy/EnemyManager;
    >>Hierarchy───create Empty───Name: ZombunnySpawnPoint;
    >>ZombunnySpawnPoint─┬─Transform─┬─Position: X-20.5, Y0, Z12.5;
                         │           ├─Rotation: X0, Y130, Z0;
                         │           └─Scale: X1, Y1, Z1;
                         ├─Duplicate───Name: ZombearSpawnPoint;
                         └─Duplicate───Name: HellephantSpawnPoint;
    >>ZombearSpawnPoint───Transform─┬─Position: X22.5, Y0, Z15;
                                    └─Rotation: X0, Y240, Z0;
    >>HellephantSpawnPoint───Transform─┬─Position: X0, Y0, Z32;
                                       └─Rotation: X0, Y230, Z32;
    >>Hierarchy/Player──>Hierarchy/EnemyManager/Enemy Manager(Script)/PlayerHealth;
    >>Prefabs/Zombunny──>Hierarchy/EnemyManager/Enemy Manager(Script)/Enemy;

###10. Game Over

    >>2D & double click HUDCanvas;
    >>HUDCanvas───right click───UI: Image───Name: ScreenFader;
    >>ScreenFader───Rect Transform───Anchor Presets─┬─shift─┬─stretch & stretch;
                                                    └─Alt───┘
    >>HUDCanvas─┬─right click───UI:Image───Name: GameOverText;
                ├─GameOverText─┬─Anchor Presets─┬─shift─┬─middle & center;
                │              │                └─Alt───┘
                │              ├─Width:300, Height:50;
                │              ├─Text(Script)─┬─Text: Game Over;
                │              │              ├─Font: LuckiestGuy;
                │              │              ├─Font Size: 50;
                │              │              ├─Alignment: center & middle;
                │              │              └─color: white;
                │              ├─Add Component: Shadow;
                │              └─Shadow───Effect Distance: X2, Y-2;
                ├─ensure the order is─┬─HealthUI
                │                     ├─DamageImage
                │                     ├─ScreenFader
                │                     ├─GameOverText
                │                     └─ScoreText
                ├─ScreenFader──┬─Color: A0;
                ├─GameOverText─┘
                ├─Window───Animation───drag next to Game;
                ├─Animation─┬─create: GameOverClip(in Animation Folder);
                │           ├─Add Curve─┬─GameOverText: Text.color;
                │           │           ├─GameOverTExt: ReactTransform.Scale;
                │           │           ├─ScreenFader: Image.Color;
                │           │           └─ScoreText: Rect Transform.Scale;
                │           └─Loop Time: ×;
                ├─Animator─┬─Create State: Empty───Name: Empty;
                │          ├─Make a Transition───(Empty->GameOverClip);
                │          └─create a trigger: GameOver;
                ├─Scripts/Manager/GameOverManager──>HUDCanvas;
                └─Hierarchy/Player──>HUDCanvas/GameOverManager/PlayerHealth;