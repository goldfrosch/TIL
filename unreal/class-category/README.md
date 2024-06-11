# 클래스 종류

특정 StaticMesh나 Actor등에 클래스를 주입해야 할 때 적합한 클래스를 주입하기 위해 각각의 종류와 용도에 대해서 알아보자

## Actor Class

월드에 배치가 가능한 클래스로 시각적 표현 기능이 존재하며, 월드에 배치하면 실제로 볼 수 있는 클래스다.

### Actor Component Class

## Pawn Class

액터 클래스에서 파생된 클래스로 Actor의 기능들을 상속받았지만 컨트롤러에 의해 소유될 수 있는 점 등의 추가 속성이 부여된다.

즉 특정 input(마우스 클릭 or 키보드 입력 등...)으로 인해 제어가 가능한 점이 있다. 즉 움직임 입력에 대한 반응이 추가되어야 한다면 Pawn이 좋은 옵션이 되곤 한다.

## Character Class

Pawn 클래스에서 파생되어 Pawn의 기능들을 모두 상속받고, 캐릭터에 특정 속성을 가지는 별도의 클래스가 존재한다.

Character Movement Component가 내장되어 있어 비행, 수영, 웅크리기 등 캐릭터 고유한 기타 움직임을 구현할 때 사용되기에 이족 보행을 하는 캐릭터에 적합하고, 이족 보행을 하지 않는다면 조금 호환이 어려울 수 있다.

즉 특정 캐릭터의 복잡한 움직임을 구현해야 한다면 Character class가 적합하다고 할 수 있다.

## Scene Component Class

월드 내 특정한 물리적 위치에 존재하는 액터 컴포넌트로 이 위치는 Transform으로 정의되며 위치, 회전, 스케일 정보가 포함된다. 씬 컴포넌트는 어태치먼트라는 기능을 활용해 트리를 형성하는 기능을 가지고 있고, 액터가 단일 씬 컴포넌트를 rootComponent로 지정이 가능하다.

즉 액터의 월드 위치, 회전, 스케일을 해당 루트 컴포넌트에서 그려짐을 말한다. 즉 이 SceneComponent는 주로 특정 좌표를 특정하고 그 좌표를 활용하는 경우에 수행되는 컴포넌트라고 할 수 있다.

### Attachment

씬 컴포넌트(USceneComponent와 그것을 상속받은 클래스)만이 서로 어태치가 가능한데, SetupAttachment를 이용해 특정 액터와의 결합 작업을 수행한다. 해당 명령어를 이용해 명령을 수행하는 액터를 params에 넣은 액터 트리에 주입시키는 작업이라 할 수 있다.

```
// ex.
TurretMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Tank Turret"));

ProjectileSpawnPoint = CreateDefaultSubobject<USceneComponent>(TEXT("Projectile Spawn Point"));
ProjectileSpawnPoint->SetupAttachment(TurretMesh);

// Result. TurretMesh > ProjectileSpawnPoint 관계로 TurretMesh안에 ProjectileSpawnPoint가 들어가게 된다.
```

## GameMode Class

GameModeBase 클래스를 상속받은 클래스로 GameModeBase 클래스는 게임의 전체적인 규칙을 다루는데 사용된다, 게임의 승리 조건 등을 할 때 주료 사용되고, GameMode의 경우 Match State라는 개념을 이용해 멀티플레이어 대진을 만드는 역할을 수행해 준다.
