## 실시간 공지사항 팝업 만들기

### 1) 구현 기능
- 사용자에 따른 alert 문구 변경
- 배포없이 (거의) 실시간으로 변화하는 UI

### 2) 기본 개념

#### (1) Firebase Remote Config

- 별도의 배포없이, 업데이트 다운로드 없이 앱 변경
- 기본값 설정 후 값 재정의
- 클라우드 기반 key-value 저장소

##### 목적
- 앱 사용자층에 변경 사항 빠르게 적용
    - 업데이트 없이 앱의 UI/UX 변경 지원
- 사용자 층의 특정 세그먼트에 앱 맞춤 설정
    - 앱 버전, 언어 등으로 분류된 사용자 세그먼트별 환경 제공
- A/B 테스트를 실행하여 앱 개선
    - 사용자 세그먼트 별로 개선 사항을 검증 후 점진적 적용


#### (2) Firebase A_B Testing

- Google Analytics, Firebase 예측을 통한 사용자 타겟팅
- 원격 구성(Remote Config), 알림작성기(Cloud Messaging) 활용
- 제품, 마케팅 실험 쉽게 실행, 분석, 확장


##### 목적

- 제품 환경 테스트 및 개선
    - 앱 동작 및 모양을 변경하여 최적의 제품 환경 확인
- 사용자의 재참여를 유도할 방안 모색
    - 앱 사용자를 늘리기에 가장 효과적인 문구와 메시징 설정
- 새로운 기능의 안전한 구현
    - 작은 규모의 사용자 집합을 대상으로 원하는 목표를 달성할 수 있는지 확인
- 예측된 사용자 그룹 타겟팅
    - 특정 행동을 할 것으로 예측된 사용자에 A/B 테스트 실시


### 3) 새롭게 알게 된 것

- alert view 를 customize 하기 위해 Xib 파일 생성 후 controller 를 생성할 때 args로 전달
```swift
NoticeViewController(nibName: "NoticeViewController", bundle: .main)
```

- 뷰 전환 애니메이션
    - UIModalTransitionStyle
        - .convertVertical:  아래에서 위로 올라오는 전환
        - .crrossDissolve: 뷰가 점점 투명해지면서 자연스러운 전환
        - .flipHorizontal: 수직 축을 기준으로 회전
        - .particalCurl: 페이지 넘기듯 전환
    - UIModalPresentationStyle
        - .currentContext : 현재 뷰의 크기에 따라 대응되서 띄움
        - .fullScreen
        - .overCurrentContext
        - .overFullScreen : 아래에 기존의 뷰가 보이면서 위에 새로운 화면이 뜸
        - .formSheet
        - .pageSheet
        - .popover

- replacingOccurrences(of:with:)
    - returns a new string in which all occurrences of a target string in the receiver are replaced by another given string
    - target: the string to replace
    - replacement: the string with which to replace target

- A/B testing 하면서 특정 이벤트 발생시 Google Analytics가 observe 하게
```swift
let confirmAction = UIAlertAction(title: "확인", style: .default) {
                // Google Analytics
                _ in
                Analytics.logEvent("promotion_alert", parameters: nil)
            }
```

- Google Analytics debug view를 Xcode에서 사용하기
1. Xcode 에서 Product > Scheme > Run Arguments
2. “-FIRDebugEnabled” 추가
https://fredriccliver.medium.com/how-to-activate-firebase-analytics-debug-view-in-xcode-f557fc299b28

- Firebase가 기기에 부여하는 auth Token 값 확인하기
```swift
Installations.installations().authTokenForcingRefresh(true) {
            result, error in
            if let error = error {
                print("ERROR")
                return
            }
            
            guard let result = result else { return }
            print("Installation auth token: \(result.authToken)")
        }
```

- A/B 테스트에서 테스트 기기 관리 통해 테스트 기기의 특정 변수를 테스트 할 수 있다
    - 이를 위해서 위의 auth Token 값 사용할 것
