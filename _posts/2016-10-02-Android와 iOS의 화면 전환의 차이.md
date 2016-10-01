---
layout: post
title: Android와 iOS 화면전환의 차이
author: miles3898
---

오늘 잠시 안드로이드 책을 보면서 간단한 앱(이라 하기 민망한 것)을 만들어봤다.<br />
안드로이드는 처음 만져보는 터라, 어렵지 않을까 싶었지만, 대충 만져보니, 운영체제에 상당히 의존하는 부분이 많았다.<br />

우선, 안드로이드 기기의 화면을 채우는 화면을 `액티비티`라 한다.<br />
액티비티가 나타난 상태에서 화면을 가로로 회전할 경우, 액티비티 뷰를 재생성한다.<br />
즉, onCreate(Bundle) 메서드개 재호출 되는 것이다.<br />
iOS는 viewDidLoad 메서드가 재호출 되지 않았다.<br />

다음으로 안드로이드에서 액티비티를 전환할 때, Intent를 생성한다.<br />


MainActivity.java<br />

```java
private int REQUEST_CODE = 0;
public static final String main_param = "io.github.sku-coderface.main_param"

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  if (requestCode != REQUEST_CODE ){
    return;
  }
  
  if (resultCode == RESULT_OK) {
    id(data == null) {
      return;
    }
  }
  Toast.makeText(MainActivity.this, data.getStringExtra(SecondActivity.RETURN_VALUE), Toast.LENGTH_SHORT).show();
}

@Override
protected void onCreate(Bundle savedInstanceState) {

  ...

  mChangeActivityButton.setOnClickListener(view -> {
    Intent intent = new Intent(MainActivity.this, SecondActivity.class);
    intent.putExtra(main_param, "This is param for SecondActivity");
    startActivityForResult(intent, REQUEST_CODE);
  });
}
```

SecondActivity.java<br />

```java

static final String RETURN_VALUE = "io.github.sku-coderface.return_value";

@Override
protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.second_activity);
  
  String param = this.getIntent().getStringExtra(MainActivity.main_param);
  
  ...
  
  mReturnButton.setOnClickListener( v -> {
    Intent data = new Intent();
    data.putExtra(RETURN_VALUE, "This is return value");
    setResult(RESULT_OK, data);
  });
}

```

위 코드와 같이 인텐트 인스턴스에 Extra를 키-값 쌍으로 넣어 전달한다.<br />

iOS에서도 userInfo와 같은 매개변수를 넘길수 있도록 허용하는 메서드가 많이 있다.<br />
그러나, 어째서인지 navigationController를 이용해서 segue를 넘길경우 데이터를 넘길 방법이라곤 없다.<br />

그래서 이땐 편법을 사용할 수 있다.<br />

ViewController.swift<br />

```swift

class ViewController: SecondViewControllerDelegate {

  override func prepare(for segue: UIStoryBoardSegue, sender: Any?) {
    let dest = segue.destination as! SecondViewController
    dest.params["firstParam"] = "This is parameter"
  }
  
  func secondViewController(_ secondViewController: SecondViewController, willPopWith data: Any?){
    Swift.print(data as! String)
  }
}

```

SecondViewController.swift<br />

```swift

@objc protocol SecondViewControllerDelegate {
  @objc optional func secondViewController(_ secondViewController: SecondViewController, willPopWith data: Any?);
}

class SecondViewController {
  var params:[String: Any]
  var delegate: SecondViewControllerDelegate?
  
  override func viewDidLoad() {
    super.viewDidLoad();
   
    delegate?.secondViewController(self, willPopWith: "This is return Value")
    Swift.print(params["firstParam"])
    navigationController.popViewController(animated: true)
  }
}

```

뭔가 아~주 불편한 감이 있지만, 프로토콜을 이용하여 델리게이트 메서드를 정의하고, 호출하는 뷰를 델리게이션 메서드를 구현해야 한다.<br />
세그먼트를 이용한 화면 전환의 한계인지 정확히는 모르겠으나, 조금 더 자세히 알아볼 필요가 있다.<br />
