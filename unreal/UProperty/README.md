# UProperty

## 종류

### VisibleAnywhere

어느 위치든 열람이 가능함을 의미하며 해당 옵션을 키면 수정은 불가능 하지만 Blueprint나 다른 외부(인스턴스)에서 조회가 가능하게 된다.

### EditAnywhere

어느 위치든 열람과 수정이 가능함을 의미하며 해당 옵션을 키게 되면 해당 액터에서 조회가 가능하면서도 수정 또한 가능해진다.

### VisibleInstanceOnly

실제로 생성된 인스턴스에서만 조회가 가능하며 해당 기능을 만드는 블루프린트에서는 조회가 불가능하다. 또한 수정은 불가능하다

### VisibleDefaultOnly

인스턴스가 아닌 영역 (Blueprint 쪽의 Detail)에서만 조회가 가능하고 수정은 불가능하다.

### EditInstanceOnly

실제로 생성된 인스턴스에서만 조회 및 수정이 가능하며 해당 기능을 만드는 블루프린트에서는 해당 기능들의 이용이 불가능하다

### EditDefaultOnly

인스턴스가 아닌 영역 (Blueprint쪽의 Detail)에서만 조회 및 수정이 가능하다.

### BlueprintReadWrite

블루프린트의 이벤트 노드 사용이 가능해지며, 조회 및 수정이 가능해진다. 다만 Detail 패널에서의 노출 여부와는 관계 없다

### BlueprintReadOnly

블루프린트의 이벤트 노드에서 사용이 가능해지며, 조회만 가능하다. 다만 Detail 패널에서의 노출 여부와는 관계 없다
