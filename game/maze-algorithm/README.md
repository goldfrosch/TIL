# 미로 만들기 알고리즘

미로 자동 생성기 (정확하게는 맵 자동 생성기에 가깝지만...)를 구축하기 위해 어떤 알고리즘으로 해석해야 할 지 더 구체적으로 이해하기 위해 다양한 알고리즘에 대해 이해해보도록 하자

## 종류

### Hunt-and-Kill

#### 동작 방식

1. 시작 위치를 선택한다.
2. 현재 타일 맵에 방문하지 않은 근접한 곳이 없을 때 까지 통로를 만들면서 이동한 타일 주변에 방문하지 않은 타일이 없을 때 까지 *무작위*로 이동하는 동작을 수행한다.
3. 2번이 완료된다면 (즉 주변에 방문하지 않은 타일이 없는 상황) 각 행 마다 새롭게 시작해야할 방문하지 않은 위치를 스캔하는 일명 "Hunt" 모드로 진입한다. 그 후 방문하지 않은 셀이 발견되면 방문한 셀 하나와 임의로 연결하고 시작 지점으로 지정한다.
4. 빈 셀이 없을 때 까지 2, 3번을 반복한다.

핵심은 무작위지만 미리 길을 만들고 새롭게 만드는 방식이라고 할 수 있다.

#### 예시 코드

주의사항: 직접 구현이기에 잘못되었을 수 있음

```
#include <bits/stdc++.h>

using namespace std;

typedef pair<int, int> pos;
#define y first
#define x second

enum Direction {
    N = 1, S = 2, E = 3, W = 4
};

struct Tile {
    bool N;
    bool S;
    bool E;
    bool W;
};

const int MAP_SCALE = 8;

Tile MAP[MAP_SCALE][MAP_SCALE];
bool isEnd = false;

pos StartPoint = {3, 4};

void Hunt(pos* point) {
    for (int i = 0; i < MAP_SCALE; i++) {
        for (int j = 0; j < MAP_SCALE; j++) {
            auto pos = MAP[i][j];
            if (!pos.N && !pos.S && !pos.E && !pos.W) {
                point->y = i;
                point->x = j;
                return;
            }
        }
    }

    isEnd = true;
}

void print() {
    for (int i = 0; i < MAP_SCALE; i++) {
        for (int j = 0; j < MAP_SCALE; j++) {
            if (MAP[i][j].N) {
                if (MAP[i][j].S) {
                    if (MAP[i][j].W) {
                        if (MAP[i][j].E) {
                            std::cout << "   ";
                        } else {
                            std::cout << " ▕ ";
                        }
                    } else if (MAP[i][j].E) {
                        std::cout << " ▏ ";
                    } else {
                        std::cout << " || ";
                    }
                } else if (MAP[i][j].W) {
                    std::cout << " ╖ ";
                } else if (MAP[i][j].E) {
                    std::cout << " ╙ ";
                } else {
                    std::cout << " U ";
                }
            } else if (MAP[i][j].S) {
                if (MAP[i][j].W) {
                    if (MAP[i][j].E) {
                        std::cout << " ▔ ";
                    } else {
                        std::cout << "  | ";
                    }
                } else if (MAP[i][j].E) {
                    std::cout << " ╒ ";
                } else {
                    std::cout << " ᚢ ";
                }
            } else if (MAP[i][j].W) {
                if (MAP[i][j].E) {
                    std::cout << " = ";
                } else {
                    std::cout << " ] ";
                }
            } else if (MAP[i][j].E) {
                std::cout << " [ ";
            } else {
                std::cout << " ▣ ";
            }
        }
        std::cout << endl;
    }
}

int main() {
    pos NowPoint = StartPoint;
    std::mt19937 engine;

    std::uniform_int_distribution<int> distribution(1, 4);
    while (!isEnd) {
        int randomNumber = distribution(engine);

        switch (randomNumber) {
            case N: {
                if (NowPoint.y > 0) {
                    if (MAP[NowPoint.y - 1][NowPoint.x].S) {
                        Hunt(&NowPoint);
                    } else {
                        MAP[NowPoint.y][NowPoint.x].N = true;
                        NowPoint.y--;
                        MAP[NowPoint.y][NowPoint.x].S = true;
                    }

                }
            }
            case S: {
                if (NowPoint.y < MAP_SCALE - 1) {
                    if (MAP[NowPoint.y + 1][NowPoint.x].N) {
                        Hunt(&NowPoint);
                    } else {
                        MAP[NowPoint.y][NowPoint.x].S = true;
                        NowPoint.y++;
                        MAP[NowPoint.y][NowPoint.x].N = true;
                    }
                }
            }
            case E: {
                if (NowPoint.x < MAP_SCALE - 1) {
                    if (MAP[NowPoint.y][NowPoint.x + 1].W) {
                        Hunt(&NowPoint);
                    } else {
                        MAP[NowPoint.y][NowPoint.x].E = true;
                        NowPoint.x++;
                        MAP[NowPoint.y][NowPoint.x].W = true;
                    }
                }
            }
            case W: {
                if (NowPoint.x > 0) {
                    if (MAP[NowPoint.y][NowPoint.x - 1].E) {
                        Hunt(&NowPoint);
                    } else {
                        MAP[NowPoint.y][NowPoint.x].W = true;
                        NowPoint.x--;
                        MAP[NowPoint.y][NowPoint.x].E = true;
                    }
                }
            }
        }
        std::cout << randomNumber << endl;
        print();
    }

    return 0;
}
```

### Recursive Backtracker (재귀 백트래커)

### Recursive Division (재귀 분할)

### Prim's

### Kruskal's

### Sidewinder

### Eller's

## 결론
