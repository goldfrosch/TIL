# Trace

보통 게임에서 내가 시야에 보이는 특정 오브젝트와 상호작용을 해야하는 상황이 오곤 한다.

하지만 코드에서는 내가 어떤 것을 보고 있는지, 어떤 오브젝트와 상호작용 해야하는지 모르고 그저 깜깜한 프레임안에 갇혀있는 것 처럼 보이기 때문에, 어두운 프레임에 빛을 비춰 탐색하는 것처럼 행동해야 한다.

Trace는 이 문제를 해결해주는 솔루션이라고 할 수 있다.

## Trace란

Trace는 레벨을 뻗어나가면서 직선 상에 무엇이 존재하는지 확인할 수 있는 메서드를 제공한다. 시작과 끝 지점을 제공해주면 Physics System에서 그 두 점에 직선을 그어 그 선 안에 포함 되어있는 액터가 있는지 탐색해 보고해주는 방식이기에 Line Trace라고 부르기도 한다.

트레이스는 본질적으로 다른 소프트웨어의 Raycast, Raytrace와 동일한 기능을 하고, 해당 기능은 FPS에서 특정 적을 탐색해 맞추는 작업을 할 때 애용하는 방식이다.

## Shape Trace

Line Trace는 말 그대로 선 안에 특정 오브젝트가 있는지 검증하는 방식이다. 선이기 때문에 매우 넓이가 좁고, 그로 인해서 매우 세밀하게 감지해 FPS에서 특정 적을 맞추는 것 에는 장점이 있지만 이것은 때로 단점으로 돌아온다.

다른 장르에서는 특정 오브젝트와 상호작용 하려면 완벽하게 똑바로 바라보아야 하기 때문이다.

이런 문제를 Shape Trace라는 대안으로 해결하곤 하는데, Line Trace와 다르게 연결짓는 모양을 선이 아닌 다른 구조로 사용하게 된다.

사용중인 구조가 구체면 Sphere Trace라고도 할 수 있지만 대개는 Geometry Trace라고 부른다.

해당 방식을 사용해 세밀하지 않고 조금 더 넓은 시야 안의 오브젝트를 잡을 수 있게 된다.

## Trace Channel

위에 Line Trace, Shape Trace를 통해 내 시야 or 특정 영역에서 Actor, Object를 탐지할 수 있는 기능은 알았지만 내가 굳이 탐색하지 않고 싶은 오브젝트 들도 있을 것이다. (ex. 바닥, 벽 등)

벽을 통과해서 무언가를 잡고싶은데 특정 오브젝트에도 막힐 수 있다.

이런 문제를 Trace Channel로 해결하곤 하는데 설정을 통해 특정 오브젝트를 Trace 환경에서 무시할 수 있도록 설정할 수 있다.
