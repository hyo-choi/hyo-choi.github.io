---
layout: post
title: "[Unity] 씬 전환시 BGM과 UI 유지하기"
categories: [STUDY, STUDY/Unity]
tags: [Unity]
comment: true
---

유튜브에서 유니티 튜토리얼을 따라하며 공부하다가 BGM을 추가해보고 싶어졌다. 그런데 GameObject로 BGMManager를 각 씬에 넣어줬더니 BGM이 이어지지 않고 씬마다 각각 재생되었다. 같은 BGM을 샘플 게임 내내 유지하고 싶었기 때문에 해결 방법을 찾아보았다.

<br>
## 씬 전환 시 오브젝트 유지하기

### DontDestroyOnLoad

DontDestroyOnLoad(GameObject)는 다른 씬으로 넘어갈 때 파괴하지 않고 유지할 오브젝트에 사용할 수 있다. 주의할 점은 DontDestroyOnLoad를 어떤 오브젝트의 하위 오브젝트에 설정하면 ```DontDestroyOnLoad only works for root GameObjects or components on root GameObjects.``` 라는 워닝을 보게 된다는 것이다. 주의 문구대로 루트 오브젝트, 혹은 루트 오브젝트의 컴포넌트에만 해당 함수를 적용하도록 하자.

또, 해당 오브젝트를 Destroy하지 않는 이상 이어지는 모든 씬에 존재하게 되므로 BGM 오브젝트와 같이 중복되면 안 되는 경우 다른 씬의 중복은 제거해주어야 한다.

- 적용 예시

```csharp
private void Awake() // playOnAwake는 설정하지 않았음
{
	BGMManager = gameObject;
	bgm = GetComponent<AudioSource>();
	pauseAndPlay();
	DontDestroyOnLoad(BGMManager); // 여기!
}
```

<br>
### BGM 오브젝트와 연동된 UI도 함께 유지하기

위 방법을 통해 다음 씬으로 전환될 때에도 BGM이 끊기지 않도록 설정하였다. 여기서 좀 더 디벨롭하여 BGM의 재생/정지 버튼을 만들어 임의로 조정할 수 있게 하고 싶었는데, Canvas에 버튼 UI를 넣어두니 씬이 전환될 때마다 DontDestroyOnLoad 처리해둔 BGMManager와 제대로 연결이 되지 않았다.

이 문제는 어떻게 해결할 수 있을지 몰라 한참을 헤맸는데, 웬걸 굉장히 간단하게 해결할 수 있는 문제였다.

Canvas 오브젝트는 루트 오브젝트여야 하고 UI 요소들은 해당 Canvas 안에만 있을 수 있다고 생각했는데, 생각해보니 Canvas를 BGMManager의 하위 오브젝트로 추가할 수 있었다. 그래서 이렇게 BGMManager의 하위에 Canvas를, 그 하위에 재생/정지를 처리해줄 버튼 오브젝트를 추가해주었다.

<p align="center">
<img src="/assets/images/2021-03-04-how-to-keep-BGM-and-UI_1.png" width="200px" alt="Hierarchy image"/> 
</p>
그러자 UI도 DontDestroyOnLoad의 영향을 받아 씬 전환시에 유지되며 BGM과의 연결도 끊어지지 않아 의도한 동작대로 잘 처리되었다.

이 경우에도 Destroy 전까지는 버튼이 모든 씬에서 출력되므로 꼭! 중복되는 버튼은 제거해주도록 한다. 왜 이 말을 두 번이나 쓰냐면 루트 Canvas 오브젝트에 추가해두었던 플레이 버튼을 안 지워서 구현이 제대로 안 된 줄 알고 1시간쯤 삽질했기 때문이다…….

- 해당 부분 코드

```csharp
private void Awake()
{
	BGMManager = gameObject;
	bgm = GetComponent<AudioSource>();
	playButton = GetComponentInChildren<Button>();
	playButtonText = playButton.GetComponentInChildren<Text>();
	pauseAndPlay();
	DontDestroyOnLoad(BGMManager);
}

public void pauseAndPlay() // inspector에서 버튼의 onClick과 연동해두었음
{
	if (bgm.isPlaying)
	{
		playButtonText.text = "▶︎";
		bgm.Pause();
	}
	else
	{
		playButtonText.text = "❚❚";
		bgm.Play();
	}
}
```

<p align="center">
<img src="/assets/images/2021-03-04-how-to-keep-BGM-and-UI_2.png" width="100%" alt="Game screenshot"/> 
완성한 샘플 게임 이미지
</p>
이렇게 BGM과 UI 처리를 할 수 있었다. DontDestroyOnLoad는 종종 쓰이게 될 것 같으므로 잘 기억해두는 것이 좋겠다.
