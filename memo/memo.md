---
layout: default
title: 메모
nav_order: 11
has_children: true
---

# xcode 프리뷰 안 나올때

---


xcode를 처음 사용해보다가

갑자기 프리뷰가 안나와서 당황한 경험을 기록하고자 합니다.

사실 알고나서는 황당했습니다.

안드로이드 Jetpack Compose처럼 preview 어노테이션을 사용하여 안드로이드 스튜디오 

오른쪽에 프리뷰 화면을 띄워놓을수 있듯이

작성하고자 하는 swift파일에서

리턴 타입이 PreviewProvider인 struct를 하나 만들면 됩니다.

```swift
struct ContentView: View {
    var body: some View {
        VStack(alignment: HorizontalAlignment.leading) {
            Text("Welcome to Stronger")
                .font(.system(size: 48))
            Spacer()
            
            Button {
                
            } label: {
                Text("Test")
                    .frame(maxWidth: .infinity, maxHeight: 48)
                    
            }
            .background(Color(red: 0.79, green: 0.99, blue: 0.43))
            .buttonStyle(.borderless)
            .cornerRadius(8)
        }
        .padding(EdgeInsets(top: 48, leading: 20, bottom: 48, trailing: 20))
    }
}

struct ContentView_Previews: PreviewProvider {
	static var previews: some View {
		ContentView()
	}
}
```