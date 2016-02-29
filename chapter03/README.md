## 챕터3
### 환경 설정
OSX에서 개발하는 것을 기준으로 하며 아직 윈도우와 리눅스 환경은 불안정함
homebrew 가 설치되어 있다고 가정하며 node, watchman, flow 를 설치한다.

```
$ brew install node
$ brew install watchman #(node 의 watch 버그로 인해 사용)
$ brew install flow #(선택사항)
```

리액트 네이티브를 설치한다.
```
$ npm install -g react-native-cli
```

iOS 는 Xcode 를 설치하고 안드로이드는 
http://facebook.github.io/react-native/docs/android-setup.htm
를 참고해서 설치한다.


프로젝트 시작은 아래와 같이 init 명령을 사용한다.

$ react-native init FirstProject

자동으로 아래와 같은 구조가 만들어진다.

```
androd
	app
	build.gradle
	gradle
	gradle.properties
	gradlew
	gradlew.bat
	settings.gradle
index.android.js
index.ios.js
ios
	프로젝트명
	프로젝트명.xcodeproj
	프로젝트명Tests
	main.jsbundle
node_modules
	react-native
package.json
```

각 플랫폼별 디렉토리에는 해당 플랫폼의 보일러플레이트가 들어있다. 리액트 코드는 index.android.js 와 index.ios.js 있고 이 파일들이 리액트 어플리케이션의 엔트리 포인트가 된다.

안드로이드 에뮬레이터가 실행된 상태에서 아래와 같이 입력하면 리액트 앱이 실행된다.

```
$ react-native run-android
```


### 윈도우 환경설정

안드로이드 SDK 설치
JDK 설치
GenyMotion 설치

$ npm install -g react-native-cli
$ react-native init AwesomeProject

$ cd AwesomeProject
$ react-native run-android

여기까지 실행하면 빌드된 apk 를 주입한다.
윈도우는 자동으로  react-native start 를 새창에서 띄우지 못하기 때문에 Genymotion 에서 실행이 제대로 이루어지지 않는다.

 $ react-native start



## View 에 컴포넌트 붙이기
리액트 네이티브 앱이 실행되면 어떻게 컴포넌트가 바인딩되고 어떤 컴포넌트가 렌더링될지는 플랫폼마다 다르다.

안드로이드의 경우 MainActivity.java 파일 내에 리액트 엔트리포인트가 아래와 같이 존재한다.

```
mReactRootView.startReactApplication(mReactInstanceManager, "FirstProject", null); 
```

안드로이드는 AppRegistry를 통해 FirstProject 라는 이름의 리액트 컴포넌트를 바운드하게 된다.

### 리액트 네이티브에서의 imports
```
import React, {
	AppRegistry,
	StyleSheet,
	Text,
	View	
} from 'react-native';
```

ES2015 의 디스트럭쳐링을 사용해서 리액트 네이티브에서 사용하는 모듈들을 가져오는 형태이다.


### FirstProject Component
각 플랫폼의 엔트리포인트가 되는 컴포넌트가 index.xxxxx.js 파일에 선언되어 있다. 네이티브가 웹버전과 다르다면 div, span 대신 Text 와 View 를 썼다는 것 뿐이다.

```
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 */
'use strict';
import React, {
	AppRegistry,
	Component,
	StyleSheet,
	Text,
	View
} from 'react-native';

class ReactNativeExample extends Component {
	render() {
		return (
			<View style={styles.container}>
				<Text style={styles.welcome}>
					Welcome to React Native!
				</Text>
				<Text style={styles.instructions}>
					To get started, edit index.android.js
				</Text>
				<Text style={styles.instructions}>
					Shake or press menu button for dev menu
				</Text>
			</View>
		);
	}
}

const styles = StyleSheet.create({
	container: {
		flex: 1,
		justifyContent: 'center',
		alignItems: 'center',
		backgroundColor: '#F5FCFF'
	},
	welcome: {
		fontSize: 20,
		textAlign: 'center',
		margin: 10
	},
	instructions: {
		textAlign: 'center',
		color: '#333333',
		marginBottom: 5
	},
});

AppRegistry.registerComponent('ReactNativeExample', () => ReactNativeExample);
```
리액트 네이티브에서의 스타일은 CSS 가 아닌 소스 하단에 선언된 StyleSheet 오브젝트를 통해 이루어진다.
오직 Text 컴포넌트만 fontSize 등의 글자 관련 속성을 갖을 수 있고 모든 레이아웃 로직은 flexbox에 의해 다뤄진다.


### 날씨 앱 만들기
React 에는 가변값인 state가 존재하고 constructor 에서 초기값을 설정해줄 수 있다. 초기값 설정은 Object를 할당하는 방식으로 한다. 이후 값이 변경되면 다시 렌더링된다.

가변값에 해당하는 state 는 아래와 같이 사용할 수 있다.

<Text style={styles.welcome}>
	You input {this.state.zip}.
</Text>

사용자 입력은 TextInput 이라는 기본 컴포넌트를 사용해서 받을 수 있다.


<TextInput
	style={styles.input}
	onSubmitEditing={this.textInputHandler} />

input 스타일은 아래와 같이 세팅해보자.

input: {
	fontSize: 20,
	borderWidth: 2,
	height: 40
}

TextInput 컴포넌트에서 onSubmitEditing 속성에 this._handleTextChange 를 연결시켰는데, 이 이벤트 핸들러는 constructor 안에 선언하면 된다.

constuctor (props) {
	super(props);

	this.state = {
		zip: ''
	};
	//ES2015 Class 에서 auto binding 을 해주지 못하기 때문에 bind 메서드를 이용해서 bind 해주어야 한다.
	this.textInputHandler = function (event) {
		this.setState({zip: event.nativeEvent.text});
	}.bind(this);
}

### 데이터 표시하기

```
'use strict';

import React, {
	Component,
	StyleSheet,
	Text,
	View
} from 'react-native';

export default class Forecast extends Component {
	constructor (props) {
		super(props);
	}

	render () {
		return (
			<View>
				<Text style={styles.bigText}>
					{this.props.main}
				</Text>
				<Text style={styles.mainText}>
					오늘 날씨 {this.props.description}
				</Text>
				<Text style={styles.bigText}>
					{this.props.temp} 도
				</Text>
			</View>
		);
	}
}

const styles = StyleSheet.create({
	bitText: {
		flex: 2,
		fontSize: 20,
		textAlign: 'center',
		margin: 10,
		color: '#336699'
	},
	mainText: {
		flex: 1,
		fontSize: 16,
		textAlign: 'center',
		color: '#777777'
	}
});
```

Forecast.js 를 위와 같이 선언한다. Forecast 컴포넌트의 경우 state를 갖지 않기 때문에 상위 컴포넌트에서 전달하는 prop 값에만 의존한다.

```
'use strict';

import React, {
	Component,
	StyleSheet,
	Text,
	TextInput,
	View
} from 'react-native';
import Forecast from './Forecast';

export class ReactWeather extends Component {
	constructor (props) {
		super(props);

		this.state = {
			zip: '',
			forecast: {
				main: '흐림',
				description: '약간의 구름이..ㅠㅠ',
				temp: '15'
			}
		};
		this.textInputHandler = function (event) {
			this.setState({zip: event.nativeEvent.text});
		}.bind(this);
	}

	render () {
		return (
			<View style={styles.container}>
				<Text style={styles.main}>
					현재 입력된 값은 -> {this.state.zip} 입니다.
				</Text>
				<TextInput
					style={styles.input}
					returnKeyType='go'
					onSubmitEditing={this.textInputHandler}
				/>
				<Forecast {...this.state.forecast} />
			</View>
		);
	}
};

const styles = StyleSheet.create({
	container: {
		flex: 1,
		justifyContent: 'center',
		alignItems: 'center',
		backgroundColor: '#4D4D4D'
	},
	main: {
		fontSize: 20,
		textAlign: 'center',
		margin: 10,
	},
	input: {
		fontSize: 20,
		borderWidth: 2,
		height: 40
	}
});
```

Forecast 컴포넌트를 import 하고 컴포넌트를 View 안에 넣어주고 props 에 값이 전달되도록 한다.


### 배경이미지 추가하기
Android 와 iOS 는 각각 리소스를 추가하는 방법이 다르지만 0.14버전부터 이미지를 통합적으로 관리하는 방법을 제공하고 있다.

단순하게 js 파일이 위치한 경로에 이미지파일을 넣고 아래와 같이 가져오면 된다.

<Image source={require('./my-icon.png')} />

다른 스크린 사이즈를 위한 @2x, @3x suffix 도 아래와 같이 지원한다.
```
.
├── button.js
└── img
    ├── check@2x.png
    └── check@3x.png
```
```
<Image source={require('./img/check.png')} />
```

이러한 방식을 통해서 플랫폼에 관계없이 이미지 리소스를 통합 관리할 수 있다.

require 안의 이미지명은 정적이어야 하므로 아래와 같은 케이스는 주의해야 한다.

```
// GOOD
<Image source={require('./my-icon.png')} />

// BAD
var icon = this.props.active ? 'my-icon-active' : 'my-icon-inactive';
<Image source={require('./' + icon + '.png')} />

// GOOD
var icon = this.props.active ? require('./my-icon-active.png') : require('./my-icon-inactive.png');
<Image source={icon} />
```

### 데이터 fetch하기

```
      fetch('http://api.openweathermap.org/data/2.5/weather?q=' + city + '&units=metric&appid=44db6a862fba0b067b1930da0d769e98')
      .then(response => response.json())
      .then((responseJSON) => {
        this.setState({
          forecast: {
            main: responseJSON.weather[0].main,
            description: responseJSON.weather[0].description,
            temp: responseJSON.main.temp
          }
        })
      })
      .catch(() => {
        this.setState({
          forecast: {
            main: '오류가 발생했습니다',
            description: '',
            temp: ''
          }
        });
      });
```

fetch 메서드를 사용하면 jQuery 를 사용해서 ajax 호출을 하는 것처럼 쉽게 데이터를 가져올 수 있고 Promise 를 리턴하기 때문에 가독성 높게 로직을 구성할 수 있다.
https://fetch.spec.whatwg.org/

