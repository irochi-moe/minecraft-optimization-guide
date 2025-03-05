# 닥치고 따라하는 Minecraft: Java Edition 서버 최적화

## 시작하기 전에
이 최적화 가이드는 최대한 보편적인 부분만을 설명합니다. 모든 최적화들이 본인의 서버에 맞을거라 생각하지 마세요. 각각의 최적화엔 전부 설명을 해뒀으니, 본인이 괜찮다고 생각하는걸 취사선택하는게 좋을겁니다.

또한 이 가이드는 바닐라 경험을 해치지 않는 것을 *"목표로 하지만"* 안보이는 여러 뒷부분에서 돌아가는 방식이 달라질 수 있습니다. 그런 이유로 특히 자동화 농장 등과 같은 것들이 바닐라와 다르게 동작할 수 있습니다.

또또한 이 가이드는 어디까지나 시작점입니다. 고수용 가이드는 추후 작성 예정입니다.

## 준비물
•	뇌<br>
•	서버를 열 컴퓨터 또는 호스팅<br>
•	[Paper](https://papermc.io/downloads/paper) 또는 [Purpur](https://purpurmc.org/download/purpur) 버킷 (*다른거 쓰지 마세요 제발. 멀티스레딩, AI 최적화 등을 주장하는 버킷의 99.9%는 개소리입니다.)<br>

## 시작 플래그
시작 플래그는 서버를 여는데 있어서 가장 중요한 시작점입니다. 그리고 이 글을 읽고있는 시점에서 당신에겐 무조건 Aikar's Flags가 정배입니다. 딴생각 하지 마세요.

`java -Xms10G -Xmx10G --add-modules=jdk.incubator.vector -XX:+UseG1GC -XX:+ParallelRefProcEnabled -XX:MaxGCPauseMillis=200 -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:G1NewSizePercent=30 -XX:G1MaxNewSizePercent=40 -XX:G1HeapRegionSize=8M -XX:G1ReservePercent=20 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4 -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem -XX:MaxTenuringThreshold=1 -Dusing.aikars.flags=https://mcflags.emc.gs -Daikars.new.flags=true -jar paper.jar --nogui`

위 시작 플래그를 복사하세요. 여러분이 수정해야할 부분은 아래 두곳입니다.

`-Xms10G -Xmx10G`<br>사용할 메모리를 지정하는 부분입니다. Xms와 Xmx는 무조건 같아야 합니다. 자신이 서버에 10GB를 할당하려면 -Xms10G -Xmx10G를 적으면 되고, 32GB를 할당하려면 -Xms32G -Xmx32G로 집어넣으면 됩니다.

`paper.jar`<br>갖고있는 버킷의 파일 이름입니다. 대체로 paper-1.21.4-188.jar 처럼 생겼습니다.

## 기본적인 시작
### server.properties
`simulation-distance = 3`<br>시뮬레이션 거리는 플레이어가 돌아다닐 떄 주변이 반응하는 거리입니다. 이 거리 안에서는 작물 등이 자라고 화로 등이 돌아갑니다. 서버 렉에 아주 큰 부분을 차지합니다. 이 값은 2보다 높고 view-distance보다 같거나 낮아야합니다.

`view-distance = 5`<br>시야 거리는 플레이어가 볼 수 있는 거리입니다. 시뮬레이션 거리보다 멀리 볼 수 있지만 작물 등이 자라거나 하는 반응은 하지 않습니다. 그냥 "보이기만" 하는겁니다. 값이 클수록 메모리를 많이 먹지만, 서버 자체의 렉에는 크게 영향을 주지 않습니다. 이 값은 simulation distance와 같거나 높아야 합니다. (2 정도의 차이를 두는것을 권장)

`max-chained-neighbor-updates = 10000`<br>연속적인 이웃 업데이트 수를 제한하고 건너뜁니다. 이를 활용한 렉머신을 방지할 수 있습니다.

## 파일별로 정리
### spigot.yml
`mob-spawn-range: 3`<br>플레이어 주변에 몹들이 스폰되는 범위입니다.이 범위가 클 수록 더 멀리서부터 몹들이 스폰되지만, 3정도로 해도 플레이어가 이상함을 눈치채기 어렵습니다. 너무 낮은 값은 몹들이 너무 가까이서부터 스폰되어 불쾌함을 느낄 수 있습니다. 이 값은 simulation-distance와 같거나 낮아야합니다.

```
entity-activation-range:
      animals: 16
      monsters: 24
      raiders: 48
      misc: 8
      water: 8
      villagers: 16
      flying-monsters: 48
```
엔티티가 반응하는 거리입니다. 이 거리에 따라 몬스터가 여러분을 눈치채고 쫓아오고, 동물이 여러분 손에 든 먹이를 들고 따라올 수 있습니다. 이 설정은 일반적으로 사용되는 몹 팜들을 고장내는 결정적인 역할을 할 수 있습니다.

```
entity-tracking-range:
      players: 48
      animals: 48
      monsters: 48
      misc: 32
      other: 64
```
엔티티가 보이는 거리입니다. 이 거리 밖에 있는 각각의 값들은 플레이어에게서 보이지 않습니다. 너무 낮게 설정하면 아무것도 없는 장소에서 갑자기 뿅 하고 나타나는 불쾌한 경험을 겪게 될 수 있습니다. 서버가 너무 감자서버라 렉이 여전히 있는 경우 players, animals, monsters의 값을 32로 줄여보세요.

`tick-inactive-villagers: false`<br>entity-activation-range 밖에 있는 주민이 활동을 할지 결정합니다. 이 설정은 주민의 재고 리필이 멈추는 문제가 발생할 수 있습니다.

```
merge-radius:
      item: 3.5
      exp: 4.0
```
아이템과 경험치가 뭉치는 범위입니다. 이 범위가 너무 낮으면 바로 옆에있는 아이템도 겹쳐지지 않거나 경험치가 합쳐지지 않을 수 있고, 오히려 너무 높으면 벽 너머의 아이템과 합쳐지거나 하는 문제가 발생할 수 도 있습니다.

`hopper-check: 8`<br>호퍼를 체크하는 틱을 조정합니다. 호퍼 내 아이템이 움직이는 기본 간격이 8이므로 확인도 자주 할 필요 없이 같은 값으로 두는게 좋습니다.

### config\paper-world-defaults.yml

```
despawn-ranges:
      ambient:
        hard: 56
        soft: 30
      axolotls:
        hard: 56
        soft: 30
      creature:
        hard: 56
        soft: 30
      misc:
        hard: 56
        soft: 30
      monster:
        hard: 56
        soft: 30
      underground_water_creature:
        hard: 56
        soft: 30
      water_ambient:
        hard: 56
        soft: 30
      water_creature:
        hard: 56
        soft: 30
```
각각의 몹들이 디스폰하는 거리를 블록 단위로 설정합니다. soft 값은 그대로 두고 hard값을 simulation distance에 맞게 조절해야 합니다. 권장 값: (simulation-distance * 16) + 8

`per-player-mob-spawns: true`<br>몹 스폰을 플레이어 주변으로 한정하여 전체적으로 낮은 몹 갯수로도 평소와 다름없는 경험을 제공할 수 있습니다.

`max-entity-collisions: 2`<br>엔티티가 서로 끼이는 최대 수를 제한합니다. 일반적으로 2가 적절합니다.

`update-pathfinding-on-block-update: false`<br>몹들이 경로를 찾는 수를 줄여 AI렉을 개선합니다. 가끔 몬스터가 렉이 걸려보이는 문제가 있을 수 있습니다.

`fix-climbing-bypassing-cramming-rule: true`<br>거미 같은 엔티티가 등반할 때 끼이는 횟수에 포함이 되지 않는 문제를 해결합니다.

```
armor-stands:
  tick: false
  do-collision-entity-lookups: false
```
갑옷거치대의 틱을 비활성화하고 충돌을 비활성화합니다. 일반적인 경우 이 두개는 비활성화해도 괜찮습니다.

```
alt-item-despawn-rate:
      enabled: true
      items:
        cobblestone: 300
        netherrack: 300
        sand: 300
        red_sand: 300
        gravel: 300
        dirt: 300
        short_grass: 300
        pumpkin: 300
        melon_slice: 300
        kelp: 300
        bamboo: 300
        sugar_cane: 300
        twisting_vines: 300
        weeping_vines: 300
        oak_leaves: 300
        spruce_leaves: 300
        birch_leaves: 300
        jungle_leaves: 300
        acacia_leaves: 300
        dark_oak_leaves: 300
        mangrove_leaves: 300
        cherry_leaves: 300
        cactus: 300
        diorite: 300
        granite: 300
        andesite: 300
        scaffolding: 600
```
특정 자주 나오는 아이템의 사라지는 시간을 기본값 5분에서 더 짧게 변경합니다. 더 빨리 사라지면 당연히 관련한 렉도 줄어듭니다.

`optimize-explosions: true`<br>폭발 알고리즘을 최적화합니다. 그냥 켜두는게 좋습니다.

`redstone-implementation: ALTERNATE_CURRENT`<br>레드스톤이 작동하는 알고리즘을 변경합니다. 이 설정은 레드스톤 관련 렉을 매우 크게 개선합니다.

`max-auto-save-chunks-per-tick:8`<br>한 틱에 자동으로 저장할 청크 수를 정합니다. 한번에 저장되는 청크의 절대 갯수를 낮춰 저장을 천천히 점진적으로 하게 해 전체적인 서버의 렉을 줄이는 효과가 있습니다. 다만 플레이어가 많아 청크가 너무 많은 경우 제때 저장되지 못하는 청크가 생겨 남은 저장될 떄 렉을 유발할 수 있습니다. 약 20명당 8씩 늘리는걸 추천합니다.

`prevent-moving-into-unloaded-chunks: true`<br>언로드된 청크에 플레이어가 들어가지 못하게 막습니다. 언로드된 청크에 플레이어가 진입할 경우 그 청크가 로딩될떄까지 나머지 작업이 중단되어 서버가 렉걸리는 결과를 낳을 수 있습니다.

```
hopper:
  ignore-occluding-blocks: true
```
호퍼가 완전한 블록(불투명 블록) 내부에 있는 컨테이너를 무시할지 여부를 결정합니다. 예를 들어, 모래나 자갈 블록 속에 있는 호퍼 마인카트가 영향을 받을 수 있습니다.

```
tick-rates:
  mob-spawner: 2
  grass-spread: 4
```
각각이 실행되는 간격을 조절합니다.

`non-player-arrow-despawn-rate: 20`<br>스켈레톤 등이 발사한 화살이 사라지는 시간을 기본값보다 빠르게 설정합니다. 어차피 못먹는 화살 남겨둘 필요 없습니다.

`creative-arrow-despawn-rate: 20`<br>크리에이티브 모드에서 발사한 화살이 사라지는 시간을 기본값보다 빠르게 설정합니다. 어차피 못먹는 화살 남겨둘 필요 없습니다.

```
entity-per-chunk-save-limit:
    area_effect_cloud: 8
    arrow: 8
    breeze_wind_charge: 8
    dragon_fireball: 3
    egg: 8
    ender_pearl: 8
    experience_bottle: 3
    experience_orb: 8
    eye_of_ender: 8
    fireball: 8
    firework_rocket: 8
    llama_spit: 3
    potion: 8
    shulker_bullet: 8
    small_fireball: 8
    snowball: 8
    spectral_arrow: 16
    trident: 16
    wind_charge: 8
    wither_skull: 4
```
각각의 엔티티가 청크에 저장디는 갯수 제한을 설정합니다.


## 최적화는 아니지만 유용한 것들

### config\paper-global.yml
```
item-validation:
  book-size:
    page-max: 1024
```
책의 페이지 수를 제한합니다. 책에 너무 많은 데이터를 넣어 플레이어를 충돌시키게 만드는 이른바 '북밴'을 방지합니다.

```
packet-limiter:
  overrides:
    ServerboundCommandSuggestionPacket:
      action: DROP
      interval: 1.0
      max-packet-rate: 15.0
    ServerboundPlaceRecipePacket:
      action: DROP
      interval: 4.0
      max-packet-rate: 5.0
```
명령어를 실행하거나 조합법을 보는 속도를 제한합니다. 제한 없이 너무 빠르게 볼 수 있게 허용하면 서버가 충돌하는 원인이 될 수 있습니다.

```
misc:
  max-joins-per-tick: 3
```
플레이어가 틱당 들어올 수 있는 수를 제한합니다. 플레이어가 많이 들어오거나 봇공격이 있을 때 서버가 렉걸리지 않게 완화해줍니다.

### purpur.yml
```
settings:
  network:
    max-joins-per-second: true
```
Purpur를 사용중일 경우 이 값을 설정해주면 플레이어가 너무 많이 들어올 떄 완화 효과를 더 크게 볼 수 있습니다.

```
world-settings:
  default:
    mobs:
      dolphin:
        disable-treasure-searching: true
```
돌고래가 보물을 찾는 행동을 비황성화합니다. 이 설정은 특히 WorldPainter로 생성된 맵에서 서버가 터지는 문제를 방지해줍니다.


## X-Ray 방지
https://docs.papermc.io/paper/anti-xray 참고. 추후 작성 예정

## 렉을 찾는 방법
추후 작성 예정

## 참고한 것들
https://github.com/YouHaveTrouble/minecraft-optimization<br>
https://github.com/YouHaveTrouble/minecraft-exploits-and-how-to-fix-them<br>
https://docs.papermc.io/paper/anti-xray<br>
https://docs.papermc.io/paper/aikars-flags<br>
https://www.birdflop.com/resources/flags/<br>
https://github.com/Obydux/Minecraft-startup-flags<br>
https://planetearth.kr/<br>
https://earthpol.com/<br>