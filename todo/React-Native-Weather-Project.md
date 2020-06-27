Title: 使用React Native创建一个天气应用
Date: 2016-03-04 14:40
Category: Web
Tags: Kotlin,学习笔记
Author: 刘理想
Summary: Kotlin语法介绍

#使用React Native创建一个天气应用

* 来自Learning React Native
* 代码地址：https://github.com/liulixiang1988/react-apps/tree/master/book_demos/WeatherProject

[TOC]

效果如下：

![react native](images/QQ20160304-0.png)

##1. 创建应用

```
react-native init WeatherProject
```

将index.ios/index.android.js文件中的初始组件放到单独的文件WeatherProject.js中。

此时的index.ios.js/index.adnroid.js如下：

```javascript
'use strict';
import React, {
  AppRegistry,
} from 'react-native';
import WeatherProject from './WeatherProject';

AppRegistry.registerComponent('WeatherProject', () => WeatherProject);
```

##2. 处理用户输入

根据用户输入的城市名获取天气（天气信息来自[和风天气](http://www.heweather.com/)）

修改WeatherProject.js

```javascript
'user strict';

import React, {
  Component,
  StyleSheet,
  Text,
  TextInput,
  View
} from 'react-native';


const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  input: {
    fontSize: 20,
    borderWidth: 2,
    height: 40,
  },
});


export default class WeatherProject extends Component {

  constructor(props){
    super(props);
    this.state = {
      city: ''
    }
  }

  _handleTextChange(event){
    console.log(event.nativeEvent.text);
    this.setState({
      city: event.nativeEvent.text
    });
  }

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          城市：{this.state.city}
        </Text>
        <TextInput style={styles.input} onSubmitEditing={this._handleTextChange.bind(this)}/>
      </View>
    );
  }
}
```

##3. 显示数据

修改WeatherProject.js添加虚拟数据：

```javascript
constructor(props){
   super(props);
   this.state = {
     city: '',
     forcast: {
       main: '阴',
       description: '多云',
       temp:20
     }
   }
}
```

添加Forecast.js:

```javascript
'user strict';

import React, {
  Component,
  StyleSheet,
  Text,
  View
} from 'react-native';


const styles = StyleSheet.create({
  bigText: {
    flex: 2,
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
    color: '#FFFFFF',
  },
  mainText: {
    flex: 1,
    fontSize: 16,
    textAlign: 'center',
    color: '#FFFFFF',
  }
});


export default class Forecast extends Component {
  render() {
    return (
      <View>
        <Text style={styles.bigText}>
          {this.props.main}
        </Text>
        <Text style={styles.mainText}>
          当前：{this.props.description}
        </Text>
        <Text style={styles.bigText}>
          {this.props.temp}度
        </Text>
      </View>
    );
  }
}
```

更新WeatherProject.js来应用Forecast:

```javascript
'user strict';

import React, {
  Component,
  Image,
  StyleSheet,
  Text,
  TextInput,
  View
} from 'react-native';


import Forecast from './Forecast';

const baseFontSize = 16;

const styles = StyleSheet.create({
  backdrop: {
    flex: 1,
    flexDirection: 'column',
  },
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  row: {
    flex: 1,
    flexDirection: 'row',
    flexWrap: 'nowrap',
    alignItems: 'flex-start',
    padding: 30,
  },
  overlay: {
    paddingTop: 5,
    backgroundColor: '#000000',
    opacity: 0.5,
    flexDirection: 'column',
    alignItems: 'center',
  },
  cityContainer: {
    flex: 1,
    borderBottomColor: '#DDDDDD',
    borderBottomWidth: 1,
    marginLeft: 5,
    marginTop: 3,
  },
  cityName: {
    width: 50,
    height: baseFontSize,
  },
  mainText: {
    flex: 1,
    fontSize: baseFontSize,
    color: '#FFFFFF',
  }

});


export default class WeatherProject extends Component {

  constructor(props){
    super(props);
    this.state = {
      city: '',
      forecast: {
        main: '阴',
        description: '多云',
        temp:20
      }
    }
  }

  _handleTextChange(event){
    console.log(event.nativeEvent.text);
    this.setState({
      city: event.nativeEvent.text
    });
  }

  render() {
    return (
      <View style={styles.container}>
        <Image source={require('image!flowers')}
               resizeMode='cover'
               style={styles.backdrop}>
          <View style={styles.overlay}>
            <View style={styles.row}>
              <View style={styles.cityContainer}>
                <TextInput
                  style={[styles.cityName, styles.mainText]}
                  returnKeyType='go'
                  onSubmitEditing={this._handleTextChange.bind(this)}/>
              </View>
              <Text style={styles.mainText}>
                天气
              </Text>
            </View>
            <Forecast 
              main={this.state.forecast.main}
              description={this.state.forecast.description}
              temp={this.state.forecast.temp}/>
          </View>
        </Image>
      </View>
    );
  }
}
```

##4. 请求网络数据

```javascript
'user strict';

import React, {
  Component,
  Image,
  StyleSheet,
  Text,
  TextInput,
  View
} from 'react-native';


import Forecast from './Forecast';

const baseFontSize = 16;

const styles = StyleSheet.create({
  backdrop: {
    flex: 1,
    flexDirection: 'column',
  },
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  row: {
    flex: 1,
    flexDirection: 'row',
    flexWrap: 'nowrap',
    alignItems: 'flex-start',
    padding: 30,
  },
  overlay: {
    paddingTop: 5,
    backgroundColor: '#000000',
    opacity: 0.5,
    flexDirection: 'column',
    alignItems: 'center',
  },
  cityContainer: {
    flex: 1,
    borderBottomColor: '#DDDDDD',
    borderBottomWidth: 1,
    marginLeft: 5,
    marginTop: 3,
  },
  cityName: {
    width: 50,
    height: baseFontSize,
  },
  mainText: {
    flex: 1,
    fontSize: baseFontSize,
    color: '#FFFFFF',
  }

});


export default class WeatherProject extends Component {

  constructor(props){
    super(props);
    this.state = {
      city: '',
      forecast: null
    }
  }

  _handleTextChange(event){
    let city = event.nativeEvent.text;
    var city_code = '';
    console.log(city);
    this.setState({
      city: city
    });
    fetch('https://api.heweather.com/x3/citylist?search=allchina&key=ef29baab02f049adaa4ce2afba6f29b9')
      .then((response)=>response.json())
      .then((responseJson)=>{
        console.log(responseJson);
        for(var item of responseJson.city_info){
          if (item.city == city){
            console.log("city");
            this._getWeatherInfo(item.id);
          }
        }
      })
      .catch((error)=>{
        console.warn(error);
      });
  }

  _getWeatherInfo(city_code){
    let url = 'https://api.heweather.com/x3/weather?cityid='+city_code+'&key=ef29baab02f049adaa4ce2afba6f29b9';
    fetch(url)
      .then((response)=>response.json())
      .then((responseJson)=>{
        let weatherInfo = responseJson["HeWeather data service 3.0"][0];
        console.log(weatherInfo);
        this.setState({
          forecast:{
            main: weatherInfo.daily_forecast[0].cond.txt_n,
            description: weatherInfo.aqi.city.qlty,
            temp: weatherInfo.daily_forecast[0].tmp.min+'~'+weatherInfo.daily_forecast[0].tmp.max
          }
        });
      })
      .catch((error)=>{console.warn(error);});
  }

  render() {
    var content = null;
    if (this.state.forecast !== null){
      content = <Forecast 
              main={this.state.forecast.main}
              description={this.state.forecast.description}
              temp={this.state.forecast.temp}/>;
    }
    return (
      <View style={styles.container}>
        <Image source={require('image!flowers')}
               resizeMode='cover'
               style={styles.backdrop}>
          <View style={styles.overlay}>
            <View style={styles.row}>
              <View style={styles.cityContainer}>
                <TextInput
                  style={[styles.cityName, styles.mainText]}
                  returnKeyType='go'
                  onSubmitEditing={this._handleTextChange.bind(this)}/>
              </View>
              <Text style={styles.mainText}>
                天气
              </Text>
            </View>
            {content}
          </View>
        </Image>
      </View>
    );
  }
}

```



