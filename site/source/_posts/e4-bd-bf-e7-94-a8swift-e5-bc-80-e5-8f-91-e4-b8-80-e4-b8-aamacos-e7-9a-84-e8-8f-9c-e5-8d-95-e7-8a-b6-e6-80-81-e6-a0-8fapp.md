---
title: 使用Swift开发一个MacOS的菜单状态栏App
tags:
  - Swift
id: 673
categories:
  - IPhone
date: 2016-07-30 14:30:06
---

猴子原创，欢迎转载。转载请注明： 转载自Cocos2Der-CSDN，谢谢！ 
原文地址: http://blog.csdn.net/cocos2der/article/details/52054107

这两天突然想看看OSX下的App开发，看了几篇文章。下面这一篇我觉得入门是非常好的。我仅转述为中文，并非原文翻译。原文地址：http://footle.org/WeatherBar/

下面开始介绍如何使用Swift开发一个Mac Menu Bar（Status Bar） App。通过做一个简单的天气app。天气数据来源于[OpenWeatherMap](http://openweathermap.org)

完成后的效果如下：
![这里写图片描述](http://img.blog.csdn.net/20160728102800287)

### 一、开始建立工程

打开Xcode，Create a New Project or File ⟶ New ⟶ Project ⟶ Application ⟶ Cocoa Application （ OS X 这一栏）。点击下一步。
![这里写图片描述](http://img.blog.csdn.net/20160728103042775)

### 二、开始代码工作

1.  打开MainMenu.xib，删除默认的windows和menu菜单。因为我们是状态栏app，不需要菜单栏，不需要主窗口。
![这里写图片描述](http://img.blog.csdn.net/20160728103229456)

2.  添加一个Menu菜单
![这里写图片描述](http://img.blog.csdn.net/20160728103404529)
删除其中默认的2个子菜单选项，仅保留1个。并将保留的这个改名为“Quit”。

3.  打开双视图绑定Outlet

- 将Menu Outlet到AppDelegate，命名为statusMenu
![这里写图片描述](http://img.blog.csdn.net/20160728103722958)

*   将子菜单Quit绑定Action到AppDelegate，命名为quitClicked
![这里写图片描述](http://img.blog.csdn.net/20160728103911287)

*   你可以删除 `@IBOutlet weak var window: NSWindow!` ，这个app中用不上。

1.  代码

- 在AppDelegate.swift中statusMenu下方添加
 `let statusItem = NSStatusBar.systemStatusBar().statusItemWithLength(NSVariableStatusItemLength)`

*   applicationDidFinishLaunching函数中添加：

    statusItem.title = "WeatherBar"
    statusItem.menu = statusMenu
    `</pre>
*   在quitClicked中添加：
    <pre>`NSApplication.sharedApplication().terminate(self)
    `</pre>

        *   此时你的代码应该如下：

    <pre>`import Cocoa

    @NSApplicationMain
    class AppDelegate: NSObject, NSApplicationDelegate {

        @IBOutlet weak var statusMenu: NSMenu!

        let statusItem = NSStatusBar.systemStatusBar().statusItemWithLength(NSVariableStatusItemLength)

        @IBAction func quitClicked(sender: NSMenuItem) {
            NSApplication.sharedApplication().terminate(self)
        }

        func applicationDidFinishLaunching(aNotification: NSNotification) {
            statusItem.title = "WeatherBar"
            statusItem.menu = statusMenu
        }

        func applicationWillTerminate(aNotification: NSNotification) {
            // Insert code here to tear down your application
        }

    }
    `</pre>

    运行，你可以看到一个状态栏了。

    ### 三、进阶一步，让App变得更好

    你应该注意到了，当你运行后，底部Dock栏里出现了一个App启动的Icon。但实际上我们也不需要这个启动icon，打开Info，添加 “Application is agent (UIElement)”为YES。
    ![这里写图片描述](http://img.blog.csdn.net/20160728104923396)

    运行一下，不会出现dock启动icon了。

    ### 四、添加状态栏Icon

    状态栏icon尺寸请使用18x18![这里写图片描述](http://img.blog.csdn.net/20160728105221149), 36x36(@2x)![这里写图片描述](http://img.blog.csdn.net/20160728105242277), 54x54(@3x)，添加这1x和2x两张图到Assets.xcassets中。
    ![这里写图片描述](http://img.blog.csdn.net/20160728105405481)

    在applicationDidFinishLaunching中，修改为如下：

    <pre>`let icon = NSImage(named: "statusIcon")
    icon?.template = true // best for dark mode
    statusItem.image = icon
    statusItem.menu = statusMenu
    `</pre>

    运行一下，你应该看到状态栏icon了。

    ### 五、重构下代码

    如果我们进一步写下去，你会发现大量代码在AppDelegate中，我们不希望这样。下面我们为Menu创建一个Controller来管理。

*   新建一个NSObject的StatusMenuController.swift, File ⟶ New File ⟶ OS X Source ⟶ Cocoa Class ⟶ Next
    ![这里写图片描述](http://img.blog.csdn.net/20160728105928671)

    代码如下：

    <pre>`// StatusMenuController.swift

    import Cocoa

    class StatusMenuController: NSObject {
        @IBOutlet weak var statusMenu: NSMenu!

        let statusItem = NSStatusBar.systemStatusBar().statusItemWithLength(NSVariableStatusItemLength)

        override func awakeFromNib() {
            let icon = NSImage(named: "statusIcon")
            icon?.template = true // best for dark mode
            statusItem.image = icon
            statusItem.menu = statusMenu
        }

        @IBAction func quitClicked(sender: NSMenuItem) {
            NSApplication.sharedApplication().terminate(self)
        }
    }
    `</pre>

*   还原AppDelegate，修改为如下：

    <pre>`// AppDelegate.swift

    import Cocoa

    @NSApplicationMain
    class AppDelegate: NSObject, NSApplicationDelegate {
        func applicationDidFinishLaunching(aNotification: NSNotification) {
            // Insert code here to initialize your application
        }
        func applicationWillTerminate(aNotification: NSNotification) {
            // Insert code here to tear down your application
        }
    }
    `</pre>

    注意，因为删除了AppDelegate中的Outlet注册，所以你需要重新连Outlet，但在这之前我们需要先做一件事。（你可以试试连接StatusMenuController中的Outlet，看看会怎么样？）

*   打开MainMenu.xib，添加一个Object。
    ![这里写图片描述](http://img.blog.csdn.net/20160728110438220)
    将该Object的Class指定为StatusMenuController
    ![这里写图片描述](http://img.blog.csdn.net/20160728110530814)
    重建Outlet到StatusMenuController，注意删除之前连接到AppDelegate的Outlet
    ![这里写图片描述](http://img.blog.csdn.net/20160728110646013)

    当MainMenu.xib被初始化的时候，StatusMenuController下的awakeFromNib将会被执行，所以我们在里面做初始化工作。

    运行一下，保证你全部正常工作了。

    ### 六、天气Api

    我们使用 [OpenWeatherMap](http://openweathermap.org)的天气数据，所以你得[注册一个账号](https://home.openweathermap.org/users/sign_up)，获取到免费的API Key。

*   添加WeatherAPI.swift， File ⟶ New File ⟶ OS X Source ⟶ Swift File ⟶ WeatherAPI.swift，加入如下代码，并使用你自己的API Key。

    <pre>`import Foundation

    class WeatherAPI {
        let API_KEY = "your-api-key-here"
        let BASE_URL = "http://api.openweathermap.org/data/2.5/weather"

        func fetchWeather(query: String) {
            let session = NSURLSession.sharedSession()
            // url-escape the query string we're passed
            let escapedQuery = query.stringByAddingPercentEncodingWithAllowedCharacters(NSCharacterSet.URLQueryAllowedCharacterSet())
            let url = NSURL(string: "\(BASE_URL)?APPID=\(API_KEY)&amp;units=imperial&amp;q=\(escapedQuery!)")
            let task = session.dataTaskWithURL(url!) { data, response, err in
                // first check for a hard error
                if let error = err {
                    NSLog("weather api error: \(error)")
                }

                // then check the response code
                if let httpResponse = response as? NSHTTPURLResponse {
                    switch httpResponse.statusCode {
                    case 200: // all good!
                        let dataString = NSString(data: data!, encoding: NSUTF8StringEncoding) as! String
                        NSLog(dataString)
                    case 401: // unauthorized
                        NSLog("weather api returned an 'unauthorized' response. Did you set your API key?")
                    default:
                        NSLog("weather api returned response: %d %@", httpResponse.statusCode, NSHTTPURLResponse.localizedStringForStatusCode(httpResponse.statusCode))
                    }
                }
            }
            task.resume()
        }
    }
    `</pre>

*   添加一个Update子菜单到Status Menu。
    ![这里写图片描述](http://img.blog.csdn.net/20160728111354459)
    绑定Action到StatusMenuController.swift，取名为updateClicked
*   开始使用WeatherAPI， 在StatusMenuController中let statusItem下面加入：
    `let weatherAPI = WeatherAPI()`,    在updateClicked中加入：
    `weatherAPI.fetchWeather("Seattle")`

    注意OSX 10.11之后请添加NSAppTransportSecurity，保证http能使用。

    运行一下，然后点击Update菜单。你会收到一个json格式的天气数据。

*   我们再调整下StatusMenuController代码, 添加一个updateWeather函数，修改后如下：

    <pre>`import Cocoa

    class StatusMenuController: NSObject {
        @IBOutlet weak var statusMenu: NSMenu!

        let statusItem = NSStatusBar.systemStatusBar().statusItemWithLength(NSVariableStatusItemLength)
        let weatherAPI = WeatherAPI()

        override func awakeFromNib() {
            statusItem.menu = statusMenu
            let icon = NSImage(named: "statusIcon")
            icon?.template = true // best for dark mode
            statusItem.image = icon
            statusItem.menu = statusMenu

            updateWeather()
        }

        func updateWeather() {
            weatherAPI.fetchWeather("Seattle")
        }

        @IBAction func updateClicked(sender: NSMenuItem) {
            updateWeather()
        }

        @IBAction func quitClicked(sender: NSMenuItem) {
            NSApplication.sharedApplication().terminate(self)
        }
    }
    `</pre>

    ### 七、解析Json

    你可以使用 SwiftyJSON，但本次我们先不使用第三方库。我们得到的天气数据如下：

    <pre>`{
        "coord": {
            "lon": -122.33,
            "lat": 47.61
        },
        "weather": [{
            "id": 800,
            "main": "Clear",
            "description": "sky is clear",
            "icon": "01n"
        }],
        "base": "cmc stations",
        "main": {
            "temp": 57.45,
            "pressure": 1018,
            "humidity": 59,
            "temp_min": 53.6,
            "temp_max": 62.6
        },
        "wind": {
            "speed": 2.61,
            "deg": 19.5018
        },
        "clouds": {
            "all": 1
        },
        "dt": 1444623405,
        "sys": {
            "type": 1,
            "id": 2949,
            "message": 0.0065,
            "country": "US",
            "sunrise": 1444659833,
            "sunset": 1444699609
        },
        "id": 5809844,
        "name": "Seattle",
        "cod": 200
    }
    `</pre>

*   在WeatherAPI.swift添加天气结构体用于解析son

    <pre>`struct Weather {
        var city: String
        var currentTemp: Float
        var conditions: String
    }
    `</pre>

*   解析son

    <pre>` func weatherFromJSONData(data: NSData) -&gt; Weather? {
            typealias JSONDict = [String:AnyObject]
            let json : JSONDict

            do {
                json = try NSJSONSerialization.JSONObjectWithData(data, options: []) as! JSONDict
            } catch {
                NSLog("JSON parsing failed: \(error)")
                return nil
            }

            var mainDict = json["main"] as! JSONDict
            var weatherList = json["weather"] as! [JSONDict]
            var weatherDict = weatherList[0]

            let weather = Weather(
                city: json["name"] as! String,
                currentTemp: mainDict["temp"] as! Float,
                conditions: weatherDict["main"] as! String
            )

            return weather
        }
    `</pre>

*   修改fetchWeather函数去调用weatherFromJSONData

    <pre>`let task = session.dataTaskWithURL(url!) { data, response, error in
            // first check for a hard error
        if let error = err {
            NSLog("weather api error: \(error)")
        }

        // then check the response code
        if let httpResponse = response as? NSHTTPURLResponse {
            switch httpResponse.statusCode {
            case 200: // all good!
                if let weather = self.weatherFromJSONData(data!) {
                    NSLog("\(weather)")
                }
            case 401: // unauthorized
                NSLog("weather api returned an 'unauthorized' response. Did you set your API key?")
            default:
                NSLog("weather api returned response: %d %@", httpResponse.statusCode, NSHTTPURLResponse.localizedStringForStatusCode(httpResponse.statusCode))
            }
        }
    }
    `</pre>

    如果此时你运行，你会收到

    <pre>`2016-07-28 11:25:08.457 WeatherBar[49688:1998824] Optional(WeatherBar.Weather(city: "Seattle", currentTemp: 51.6, conditions: "Clouds"))
    `</pre>

*   给Weather结构体添加一个description

    <pre>`struct Weather: CustomStringConvertible {
        var city: String
        var currentTemp: Float
        var conditions: String

        var description: String {
            return "\(city): \(currentTemp)F and \(conditions)"
        }
    }
    `</pre>

    再运行试试。

    ### 八、Weather用到Controller中

*   在 WeatherAPI.swift中增加delegate协议

    <pre>`protocol WeatherAPIDelegate {
        func weatherDidUpdate(weather: Weather)
    }
    `</pre>

*   声明`var delegate: WeatherAPIDelegate?`
*   添加初始化

    <pre>`init(delegate: WeatherAPIDelegate) {
        self.delegate = delegate
    }
    `</pre>

*   修改fetchWeather

    <pre>`let task = session.dataTaskWithURL(url!) { data, response, error in
        // first check for a hard error
        if let error = err {
            NSLog("weather api error: \(error)")
        }

        // then check the response code
        if let httpResponse = response as? NSHTTPURLResponse {
            switch httpResponse.statusCode {
            case 200: // all good!
                if let weather = self.weatherFromJSONData(data!) {
                    self.delegate?.weatherDidUpdate(weather)
                }
            case 401: // unauthorized
                NSLog("weather api returned an 'unauthorized' response. Did you set your API key?")
            default:
                NSLog("weather api returned response: %d %@", httpResponse.statusCode, NSHTTPURLResponse.localizedStringForStatusCode(httpResponse.statusCode))
            }
        }
    }
    `</pre>

*   StatusMenuController添加WeatherAPIDelegate

    <pre>`class StatusMenuController: NSObject, WeatherAPIDelegate {
    ...
      var weatherAPI: WeatherAPI!

      override func awakeFromNib() {
        ...
        weatherAPI = WeatherAPI(delegate: self)
        updateWeather()
      }
      ...
      func weatherDidUpdate(weather: Weather) {
        NSLog(weather.description)
      }
      ...
    `</pre>

*   Callback实现，修改WeatherAPI.swift中fetchWeather：
    `func fetchWeather(query: String, success: (Weather) -&gt; Void) {`
    修改fetchWeather内容

    <pre>`let task = session.dataTaskWithURL(url!) { data, response, error in
        // first check for a hard error
        if let error = err {
            NSLog("weather api error: \(error)")
        }

        // then check the response code
        if let httpResponse = response as? NSHTTPURLResponse {
            switch httpResponse.statusCode {
            case 200: // all good!
                if let weather = self.weatherFromJSONData(data!) {
                    success(weather)
                }
            case 401: // unauthorized
                NSLog("weather api returned an 'unauthorized' response. Did you set your API key?")
            default:
                NSLog("weather api returned response: %d %@", httpResponse.statusCode, NSHTTPURLResponse.localizedStringForStatusCode(httpResponse.statusCode))
            }
        }
    }
    `</pre>

*   在controller中

    <pre>`func updateWeather() {
        weatherAPI.fetchWeather("Seattle, WA") { weather in
            NSLog(weather.description)
        }
    }
    `</pre>

    运行一下，确保都正常。

    ### 九、显示天气

    在MainMenu.xib中添加子菜单 “Weather”（你可以添加2个Separator Menu Item用于子菜单分割线）
    ![这里写图片描述](http://img.blog.csdn.net/20160728113405031)

    在updateWeather中，替换NSLog:

    <pre>`if let weatherMenuItem = self.statusMenu.itemWithTitle("Weather") {
        weatherMenuItem.title = weather.description
    }
    `</pre>

    运行一下，看看天气是不是显示出来了。

    ### 十、创建一个天气视图

    打开MainMenu.xib，拖一个Custom View进来。

*   拖一个Image View到Custom View中，设置ImageView宽高度为50。
    ![这里写图片描述](http://img.blog.csdn.net/20160728113814071)
*   拖两个Label进来，分别为City和Temperature
    ![这里写图片描述](http://img.blog.csdn.net/20160728113930033)
*   创建一个名为WeatherView的NSView，New File ⟶ OS X Source ⟶ Cocoa Class
    在MainMenu.xib中，将Custom View的Class指定为WeatherView
    ![这里写图片描述](http://img.blog.csdn.net/20160728114119091)
*   绑定WeatherView Outlet：

    <pre>`import Cocoa

    class WeatherView: NSView {
        @IBOutlet weak var imageView: NSImageView!
        @IBOutlet weak var cityTextField: NSTextField!
        @IBOutlet weak var currentConditionsTextField: NSTextField!
    }
    `</pre>

    并添加update:

    <pre>`func update(weather: Weather) {
        // do UI updates on the main thread
        dispatch_async(dispatch_get_main_queue()) {
            self.cityTextField.stringValue = weather.city
            self.currentConditionsTextField.stringValue = "\(Int(weather.currentTemp))°F and \(weather.conditions)"
            self.imageView.image = NSImage(named: weather.icon)
        }
    }
    `</pre>

    注意这里使用dispatch_async调用UI线程来刷新UI，因为后面调用此函数的数据来源于网络请求子线程。

*   StatusMenuController添加weatherView outlet

    <pre>`class StatusMenuController: NSObject {
        @IBOutlet weak var statusMenu: NSMenu!
        @IBOutlet weak var weatherView: WeatherView!
        var weatherMenuItem: NSMenuItem!
        ...
    `</pre>

*   子菜单Weather绑定到视图

    <pre>`weatherMenuItem = statusMenu.itemWithTitle("Weather")
    weatherMenuItem.view = weatherView
    `</pre>

*   update中：

    <pre>`func updateWeather() {
        weatherAPI.fetchWeather("Seattle, WA") { weather in
            self.weatherView.update(weather)
        }
    }
    `</pre>

    运行一下。

    ### 十一、添加天气图片

    先添加天气素材到Xcode，天气素材可以在http://openweathermap.org/weather-conditions 这里找到。这里我已经提供了一份[icon zip](http://footle.org/WeatherBar/assets/weather-icons.zip), 解压后放Xcode。
    ![这里写图片描述](http://img.blog.csdn.net/20160728115142069)

*   WeatherAPI.swift的Weather struct中，添加 `var icon: String`
*   在weatherFromJSONData中：

    <pre>`let weather = Weather(
        city: json["name"] as! String,
        currentTemp: mainDict["temp"] as! Float,
        conditions: weatherDict["main"] as! String,
        icon: weatherDict["icon"] as! String
    )
    `</pre>

*   在weatherFromJSONData：

    <pre>`let weather = Weather(
        city: json["name"] as! String,
        currentTemp: mainDict["temp"] as! Float,
        conditions: weatherDict["main"] as! String,
        icon: weatherDict["icon"] as! String
    )
    `</pre>

*   在WeatherView的update中：

    <pre>`imageView.image = NSImage(named: weather.icon)
    `</pre>

    运行一下，Pretty!

    ![这里写图片描述](http://img.blog.csdn.net/20160728115416945)

    ### 十二、添加设置

    在MainMenu.xib MenuItem中，添加一个Menu Item命名为“Preferences...”
    并绑定action，命名为“preferencesClicked”

*   添加NSWindowController命名为PreferencesWindow.swift New ⟶ File ⟶ OS X Source ⟶ Cocoa Class , 勾选同时创建XIB.在XIB中添加Label和Text Field。效果如下：
    ![这里写图片描述](http://img.blog.csdn.net/20160728115751650)

    Outlet cityTextField到PreferencesWindow.swift

*   在PreferencesWindow.swift中添加：

    <pre>`override var windowNibName : String! {
        return "PreferencesWindow"
    }
    `</pre>

*   windowDidLoad()中修改：

    <pre>`self.window?.center()
    self.window?.makeKeyAndOrderFront(nil)
    NSApp.activateIgnoringOtherApps(true)
    `</pre>

*   最终PreferencesWindow.swift如下：

    <pre>`import Cocoa

    class PreferencesWindow: NSWindowController {
        @IBOutlet weak var cityTextField: NSTextField!

        override var windowNibName : String! {
            return "PreferencesWindow"
        }

        override func windowDidLoad() {
            super.windowDidLoad()

            self.window?.center()
            self.window?.makeKeyAndOrderFront(nil)
            NSApp.activateIgnoringOtherApps(true)
        }
    }
    `</pre>

*   StatusMenuController.swift中添加preferencesWindow
    `var preferencesWindow: PreferencesWindow!`
*   awakeFromNib中，注意在updateWeather()之前：
    `preferencesWindow = PreferencesWindow()`
*   preferencesClicked中：
    `preferencesWindow.showWindow(nil)`
*   下面为 preferences window 添加NSWindowDelegate，刷新视图。
    `class PreferencesWindow: NSWindowController, NSWindowDelegate {`
    并增加

    <pre>`func windowWillClose(notification: NSNotification) {
        let defaults = NSUserDefaults.standardUserDefaults()
        defaults.setValue(cityTextField.stringValue, forKey: "city")
    }
    `</pre>

    增加协议:

    <pre>`protocol PreferencesWindowDelegate {
        func preferencesDidUpdate()
    }
    `</pre>

    增加delegate:

    <pre>`var delegate: PreferencesWindowDelegate?
    `</pre>

    在windowWillClose最下面调用

    <pre>`delegate?.preferencesDidUpdate()
    `</pre>

*   回到StatusMenuController中，添加PreferencesWindowDelegate

    <pre>`class StatusMenuController: NSObject, PreferencesWindowDelegate {
    `</pre>

    实现代理：

    <pre>`func preferencesDidUpdate() {
        updateWeather()
    }
    `</pre>

    awakeFromNib中：

    <pre>`preferencesWindow = PreferencesWindow()
    preferencesWindow.delegate = self
    `</pre>

*   在StatusMenuController中增加默认城市
    let DEFAULT_CITY = "Seattle, WA"
*   修改updateWeather

    <pre>`func updateWeather() {
        let defaults = NSUserDefaults.standardUserDefaults()
        let city = defaults.stringForKey("city") ?? DEFAULT_CITY
        weatherAPI.fetchWeather(city) { weather in
            self.weatherView.update(weather)
        }
    }
    `</pre>

*   咱们也可以在PreferencesWindow.swift windowDidLoad中设置city默认值

    <pre>`let defaults = NSUserDefaults.standardUserDefaults()
    let city = defaults.stringForKey("city") ?? DEFAULT_CITY
    cityTextField.stringValue = city

运行。一切OK。

其他：
- 你也可以试试使用`NSRunLoop.mainRunLoop().addTimer(refreshTimer!, forMode: NSRunLoopCommonModes)` 来定时updateWeather.
- 试试点击天气后跳转到天气中心 `NSWorkspace.sharedWorkspace().openURL(url: NSURL))`
- 完整工程： [WeatherBar](https://github.com/PlayApple/WeatherBar)