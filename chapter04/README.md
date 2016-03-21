## 챕터4

### HTML 엘리먼트와 네이티브 컴포넌트의 유사성
HTML과 달리 네이티브에서는 이와 유사한 대체 컴포넌트들을 사용한다. div 대신 View를, img 대신 Image 를 사용하는 것이다. 따라서 네이티브와 웹 간에 코드를 공유하기는 어렵지만 뷰와 비즈니스 로직을 독립적으로 구성한다면 이 부분은 공유할 수 있다.

### Text 컴포넌트
Text 컴포넌트만 텍스트를 넣을 수 있다.

```jsx
<Text>
	The quick <Text style={{fontStyle: "italic"}}>brown</Text> fox
	jumped over the lazy <Text style={{fontWeight: "bold"}}>dog</Text>.
</Text>
```

위와 같이 Text 안에 Text 컴포넌트를 중첩시켜 다른 속성을 가진 텍스트를 보여줄 수 있지만 아래와 같이 해당 속성을 가진 컴포넌트를 만드는 것이 더 효율적이다.

```jsx
let styles = StyleSheet.create({
	bold: {
		fontWeight: "bold"
	}
});

export class Strong extends Component {
	constructor (props) {
		super(props);
	}

	render () {
		return (
			<Text style={styles.bold}>
				{this.props.children}
			</Text>
		);
	}
}
```

```
<Text>
	The quick brown fox jumped
	over the lazy <Strong>dog</Strong>.
</Text>
```

또 리액트 네이티브에는 H1, H2 같은 요소가 없기 때문에 이러한 텍스트 컴포넌트를 만들어서 사용하면 된다.

## Image 컴포넌트
이미지 컴포넌트는 아래와 같이 사용할 수 있고 @2x, @3x 와 같은 프리픽스를 통해 resolution 을 지원할 수 있다.

```jsx
<Image source={require('image!puppies')} />
```

`image!` 문법을 사용해서 assets 디렉토리에 대해 접근하게 된다. (?)
웹에 있는 이미지도 아래와 같이 가져올 수 있지만, 미리 크기를 지정해주어야 한다.

```jsx
<Image
	source={{uri: 'https://facebook.github.io/react/img/logo_og.png'}}
	style={{width: 400, height: 400}}
/>
```

백그라운드 이미지를 넣는 것은 앞서 챕터에서 살펴본 것 처럼 아래와 같이 이미지 컴포넌트로 감싸서 구현한다.

```jsx
<Image source={require('image!puppies')}>
	{/* Your content here... */}
</Image>
```

`resizeMode` 라는 속성에 Contain (그대로), Cover (같은 비율로 꽉 채우기), Stretch (비율 무시하고 꽉채우기) 와 같은 값을 설정해줄 수 있다.

### 터치와 제스쳐
`TouchableHighlight` 를 사용해서 터치 관련 기능을 제어해줄 수 있다.

```jsx

<TouchableHighlight
	onPressIn={this._onPressIn}
	onPressOut={this._onPressOut}
	style={styles.touchable} >
	<View style={styles.button}>
		<Text style={styles.welcome}>
			{this.state.pressing ? 'EEK!' : 'PUSH ME'}
		</Text>
	</View>
</TouchableHighlight>
```

TouchableHighlight 로 감싸면 별다른 터치 관련 로직 없이 터치 관련 작업을 해줄 수 있다.

### GestureResponder 시스템
단순히 탭하는 것 이상의 컨트롤러 필요한 경우를 위해 React Native 에서는 GestureResponder 와 PanResponder 를 제공해주고 있다.

모바일에서는 멀티터치 등으로 인해 터치 관련 처리가 복잡하다.

View.props.onStartShouldSetResponder
View.props.onMoveShouldSetResponder
View.props.onResponderGrant
View.props.onResponderReject

기본적으로 터치가 시작되면 View.props.onStartShouldSetResponder/View.props.onMoveShouldSetResponder 에 의해 어떤 동작인지 규정되고 이후 Grant 인지 Reject 인지를 시도하게 된다. (?)

... 여러가지 Gesture Responder 관련 속성값들 ...

이는 매우 로우레벨 API로 이를 통해 터치를 제어하려고 한다면 많은 시간을 들여 튜닝하며 적당한 값들로 제스쳐들을 규정하는 과정이 요구된다.
이보다는 좀더 하이레벨로 구현된 PanResponder 를 확인해보자.

### PanResponder
`TouchableHighlight` 와 달리 PanResponder 는 컴포넌트가 아니고 React Native 가 제공해주는 클래스이다. Gesture Responder 시스템이 전달해주는 기본적인 이벤트보다는 좀더 높은 레벨의 API를 제공해준다.

http://facebook.github.io/react-native/docs/panresponder.html#content

```jsx
this._panResponder = PanResponder.create({
	onStartShouldSetPanResponder: this._handleStartShouldSetPanResponder,
	onMoveShouldSetPanResponder: this._handleMoveShouldSetPanResponder,
	onPanResponderGrant: this._handlePanResponderGrant,
	onPanResponderMove: this._handlePanResponderMove,
	onPanResponderRelease: this._handlePanResponderEnd,
	onPanResponderTerminate: this._handlePanResponderEnd
});
```

위와 같이 PanResponder 를 선언해준 뒤,

```jsx
render () {
	return (
		<View
			{...this._panResponder.panHandlers}>
			{ /* View contents here */ }
		</View>
	);
}
```

`PanResponder.create` 를 호출할 때 전달한 핸들러들은 터치 반응이 일어날 때 실행되게 된다.

tab 정도만 확인하는 간단한 경우에는 `TouchableHighlight`, 직접 터치 인터페이스를 만들고자 한다면, Gesture Responder system 또는 PanResponder 를 활용해서 구현한다. 대부분의 경우에는 `TouchableHighlight` 로도 충분할 것이다.


### ListView
리액트 네이티브의 ListView 는 datasource 와 renderRow 두 개의 props 를 필요로 한다. 렌더링하려는 데이터와 데이터를 기준으로 렌더링한 뷰가 필요한 것이다.

```jsx
render n() {
	return (
		<ListView
			dataSource={this.state.dataSource}
			renderRow={this._renderRow}
			renderHeader={this._renderHeader}
			renderFooter={this._renderFooter}
		/>
	);
}
```
추가적으로 헤더와 풋터의 뷰에 대한 렌더링 메서드도 지정해줄 수 있다.

```jsx
class ListViewTest extends Component {
	constructor (props) {
		super(props);

		this.state = {
			dataSource: new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2}).cloneWithRows([])
		};

		this._renderRow = (rowData) => {
			return (
				<View style={styles.listItem}>
					<Text style={styles.main}>
						{JSON.stringify(rowData.weather[0].main)}
					</Text>
				</View>
			);
		}

		this._renderHeader = () => {
			return (
				<View>
					<Text>
						헤더
					</Text>
				</View>
			);
		}

		this._renderFooter = () => {
			return (
				<View>
					<Text>
						풋터
					</Text>
				</View>
			);
		}

		this._refreshData = () => {
			let weatherAPI = `http://api.openweathermap.org/data/2.5/forecast?q=seoul&mode=json&appid=44db6a862fba0b067b1930da0d769e98&units=metric`;

			fetch(weatherAPI)
			.then(response => response.json())
			.then((responseJSON) => {
				this.setState({
					dataSource: this.state.dataSource.cloneWithRows(responseJSON.list)
				});
			});
		}
	}

	componentDidMount () {
		this._refreshData();
	}

	render () {
		return (
			<View style={styles.container}>
				<ListView
					dataSource={this.state.dataSource}
					renderRow={this._renderRow}
					renderHeader={this._renderHeader}
					renderFooter={this._renderFooter}
				/>
			</View>
		);
	}
};

const styles = StyleSheet.create({
	container: {
		flex: 1,
		justifyContent: 'center',
		alignItems: 'center'
	},
	main: {
		fontSize: 20,
		textAlign: 'center',
		margin: 10,
	},
	listItem: {
		flex: 1,
		flexDirection: 'row',
		backgroundColor: '#FFFFFF',
		borderBottomColor: '#336699',
		borderBottomWidth: 2,
		padding: 5
	}
});

```

### Platform-Specic Components
기본적으로는 NavigatorIOS 와 같이 특정 플랫폼을 위한 컴포넌트 보다는 Navigator 와 같이 범용 컴포넌트를 사용하는 것이 바람직하지만 안드로이드나 iOS 각각의 디자인 가이드가 존재하기 때문에 이를 따르기 위해서는 특정 플랫폼을 위한 컴포넌트를 사용해야하는 경우도 생긴다. 이때 활용하는 방법이 플랫폼의 suffix 를 사용하는 것이다.

```jsx
//Switch.ios.js
var React = require('react-native');
var { SwitchIOS } = React;
var Switch = React.createClass({
	getInitialState () {
		return {
			value: false
		};
	},

```

```jsx
//Switch.android.js
var React = require('react-native');
var { SwitchAndroid } = React;
var Switch = React.createClass({
	getInitialState () {
		return {
			value: false
		};
	},

```

위와 같이 각 플랫폼 별로 컴포넌트를 만들고 사용하는 영역에서는 아래와 같이 가져오면 플랫폼별로 특화된 컴포넌트를 사용할 수 있다.
물론 `require` 대신 `import` 로 가져와도 동일하다.

```jsx
var Switch = require('./switch');
...
```

이렇게 일부 컴포넌트만 특정 플랫폼을 위한 컴포넌트를 사용하는 형태로 만들어도 코드 재사용과 해당 플랫폼에 맞는 형태의 UI 를 구성하는데 어려움이 없을 것이다.
