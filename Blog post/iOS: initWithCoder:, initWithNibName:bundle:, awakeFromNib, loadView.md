# iOS: initWithCoder:, initWithNibName:bundle:, awakeFromNib, loadView

February 12, 2015

### <http://www.quora.com/Cocoa-API/What-is-the-difference-between-initWithCoder-initWithNibName-and-awakeFromNib>

Two of these methods are initializers, and the third is a method invoked as part of the nib-loading process.

두개의 이니셜라이저가 있다. 그리고 새번재 의 nib의 부분을 로드하는 프로세스를 호출하는 메소드가 있다.



**initWithCoder:**

이 메소드는 아카이빙된 오브젝트를 초기화하는 이니셜라이저이다.

오브젝트들을 아카이빙하여 nib에 저장하기 때문에, **nib으로 오브젝트를 로드해올 때 사용되는 이니셜라이저이다**.

nib으로 부터 오브젝트로 역직렬화 한다. 그러나 아직 IBOutlet/IBAction은 연결되지 않는다.

`UIViewController`가 nib으로 부터 생성되어질 때, 호출된다.

<br>

**initWithNibName:bundle:**

UIViewController의 지정 초기화 메소드이다. **코드로 `UIViewController` 를 생성할 때, 호출된다.**

이 메소드는 UIViewController를 nib에서 호출할 수 있도록 설정합니다.

This method sets up UIViewController to be able to load a nib on demand (namely, when it’s view property is accessed).

**awakeFromNib**
This method is called on every single object that is unarchived from a nib file, after all outlets/actions have been set up. **If initWithCoder: is the beginning of the nib unarchiving process, then awakeFromNib is the end**. It signals to the object that all objects have been unarchived and that all of its connections are available. In the context of UIViewController, it signals to the view controller that it’s now ready to be used.

When creating a UIViewController, you have two options. The first is to create it in code. If you go this way, you use initWithNibName:bundle:. The second is to create one by loading a nib. If you go this way, the nib loading process automatically invokes both initWithCoder: and awakeFromNib.

It’s important to note that initWithCoder:/awakeFromNib are not unique to UIViewController, while initWithNibName:bundle: is. If you’re implementing a UIView subclass you’ll encounter the same situation, except this time you have initWithCoder:/awakeFromNib and initWithFrame:. In general, objects unarchived from a nib use initWithCoder:/awakeFromNib, and objects created in code use their designated initializer.

A common trick to dealing with this sort of situation is to create a “common init” method that’s called from both of the initializers:

> – (void)commonInit {
> _myIvar = @”some value”;
> _anotherIvar = 3;
> }
>
> – (id)initWithCoder:(NSCoder *)aDecoder {
> if ((self = [super initWithCoder:aDecoder])) {
> [self commonInit];
> }
> return self;
> }
>
> – (id)initWithFrame:(CGRect)frame {
> if ((self = [super initWithFrame:frame])) {
> [self commonInit];
> }
> return self;
> }

**-(void)loadView //if manually generating the view contoller**

 

 

### **https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/BasicViewControllers/BasicViewControllers.html#//apple_ref/doc/uid/TP40007457-CH101**

### Use a Storyboard to Implement Your View Controller

You might consider whether or not to use a storyboard as an implementation detail, but the approach you take affects how you implement the view controller and how other objects collaborate with it. You always use a storyboard unless you have a strong reason not to.

#### When you use storyboards:

- iOS usually instantiates your view controller for you automatically.
- To finish instantiating it, you override its **awakeFromNib** method.
- Other objects configure it through its properties.
- You create its view hierarchy and other related objects in Interface Builder. These objects are loaded automatically when the view is needed.
- Relationships with other view controllers are created in the storyboard.

#### If you design your view controller to be used programmatically:

- The view controller is instantiated by allocating and initializing it.
- You create an custom initialization method to initialize the view controller.
- Other objects configure the view controller using its initialization method and by configuring its properties.
- You override the **loadView** method to programmatically create and configure its view hierarchy.
- Relationships with other view controllers are created by writing code.







**NaviController init(coder:) Start**

- **MainViewController init(coder:) Start**

- **MainViewController init(coder:) End**

**NaviController init(coder:) End**



**NaviController loadView() Start**

- **NaviController loadView()  view = Optional(<UILayoutContainerView: 0x7ff793804ee0; frame = (0 0; 414 896); clipsToBounds = YES; autoresize = W+H; gestureRecognizers = <NSArray: 0x6000030612c0>; layer = <CALayer: 0x600003e58f60>>)**

**NaviController loadView() End**



**NaviController viewDidLoad() Start**

**NaviController viewDidLoad() End**



**NaviController viewWillAppear(_:) Start**

**NaviController viewWillAppear(_:) End**



**MainViewController loadView() Start**

- **MainView init(coder:) Start**

  - **MainView layerClass Start**

    - **RedView init(coder:) Start**

      - **RedView layerClass Start**
        - **GreenView init(coder:) Start**
          - **GreenView layerClass Start**
        - **GreenView init(coder:) End**
      - **RedView addSubview(_:) Start**
        - **GreenView willMove(toSuperview:) Start**
        - **GreenView willMove(toSuperview:) End**
        - **GreenView didMoveToSuperview() Start**
        - **GreenView didMoveToSuperview() End**

      - **RedView addSubview(_:) End**

      

      - **RedView addConstraints(_:) Start**
      - **RedView addConstraints(_:) End**

    -  **RedView init(coder:) End**

      

    - **BlueView init(coder:) Start**

      - **BlueView layerClass Start**

    - **BlueView init(coder:) End**

    

  - **MainView addSubview(_:) Start**

    - **RedView willMove(toSuperview:) Start**
    - **RedView willMove(toSuperview:) End**
    - **RedView didMoveToSuperview() Start**
    - **RedView didMoveToSuperview() End**

  - **MainView addSubview(_:) End**

    

  - **MainView addSubview(_:) Start**

    - **BlueView willMove(toSuperview:) Start**

    - **BlueView willMove(toSuperview:) End**

      

    - **BlueView didMoveToSuperview() Start**

    - **BlueView didMoveToSuperview() End**

  - **MainView addSubview(_:) End**

    

  - **MainView addConstraints(_:) Start**
  - **MainView addConstraints(_:) End**

- **MainView init(coder:) End**



- **MainViewController loadView() **
  - **view = Optional(<ViewLifeCycleTest.MainView: 0x7ff79160d7b0; frame = (0 0; 414 896); autoresize = W+H; layer = <CALayer: 0x600003e58380>>)**

- **MainViewController loadView() End**



- **MainViewController viewDidLoad() Start**

- **MainViewController viewDidLoad() End**

  

- **MainView willMove(toSuperview:) Start**

- **MainView willMove(toSuperview:) End**

  

- **MainView didMoveToSuperview() Start**

- **MainView didMoveToSuperview() End**



- **MainViewController viewWillAppear(_:) Start**

- **MainViewController viewWillAppear(_:) End**

  

- **GreenView didMoveToWindow() Start**

- **GreenView didMoveToWindow() End**

  

- **RedView didMoveToWindow() Start**

- **RedView didMoveToWindow() End**

  

- **BlueView didMoveToWindow() Start**

- **BlueView didMoveToWindow() End**

  

- **MainView didMoveToWindow() Start**

- **MainView didMoveToWindow() End**



- **GreenView updateConstraints() Start**

- **GreenView updateConstraints() End**



- **RedView updateConstraints() Start**

- **RedView updateConstraints() End**



- **BlueView updateConstraints() Start**

- **BlueView updateConstraints() End**



- **MainViewController updateViewConstraints() Start**
  - **MainView updateConstraints() Start**

  - **MainView updateConstraints() End**

- **MainViewController updateViewConstraints() End**



- **NaviController updateViewConstraints() Start**

- **NaviController updateViewConstraints() End**



- **MainView layerWillDraw(_:) Start**

- **MainView layerWillDraw(_:) End**



- **MainView draw(_:) Start**

- **MainView draw(_:) End**



- **BlueView layerWillDraw(_:) Start**

- **BlueView layerWillDraw(_:) End**

- **BlueView draw(_:) Start**

- **BlueView draw(_:) End**

  

- **RedView layerWillDraw(_:) Start**

- **RedView layerWillDraw(_:) End**

- **RedView draw(_:) Start**

- **RedView draw(_:) End**



- **GreenView layerWillDraw(_:) Start**

- **GreenView layerWillDraw(_:) End**

- **GreenView draw(_:) Start**

- **GreenView draw(_:) End**



- **NaviController viewDidAppear(_:) Start**
  - **MainViewController viewDidAppear(_:) Start**

  - **MainViewController viewDidAppear(_:) End**
  - **MainViewController didMove(toParent:) Start**
  - **MainViewController didMove(toParent:) End**

- **NaviController viewDidAppear(_:) End**



<iframe id="dsq-app2971" name="dsq-app2971" allowtransparency="true" frameborder="0" scrolling="no" tabindex="0" title="Disqus" width="100%" src="https://disqus.com/embed/comments/?base=default&amp;f=suhooh&amp;t_i=248%20http%3A%2F%2Fsuho.berlin%2F%3Fp%3D248&amp;t_u=http%3A%2F%2Fsuho.berlin%2Fengineering%2Fios%2Fios-initwithcoder-initwithnibnamebundle-awakefromnib-loadview%2F&amp;t_e=iOS%3A%20initWithCoder%3A%2C%20initWithNibName%3Abundle%3A%2C%20awakeFromNib%2C%20loadView&amp;t_d=iOS%3A%20initWithCoder%3A%2C%20initWithNibName%3Abundle%3A%2C%20awakeFromNib%2C%20loadView&amp;t_t=iOS%3A%20initWithCoder%3A%2C%20initWithNibName%3Abundle%3A%2C%20awakeFromNib%2C%20loadView&amp;s_o=default#version=6546132fc1e26b55e9024fd962f2a03b" horizontalscrolling="no" verticalscrolling="no" style="box-sizing: border-box; margin-bottom: 1.75em; max-width: 100%; vertical-align: middle; width: 1px !important; min-width: 100%; border: none !important; overflow: hidden !important; height: 729px !important;"></iframe>