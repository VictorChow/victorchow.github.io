---
layout: post
title: React Native Snippets
date: 2016-12-15
categories: code
tags: ReactNative VSCode
---

> VS Code开发React Native时的Snippets

```javascript
{
    "React Native Component File":{
        "prefix":"_rnc",
        "body":[
            "import React, { Component } from 'react'",
            "import {",
            " View,",
            " Text,",
            " StyleSheet,",
            "} from 'react-native' ",
            "export default class ${1:NewComponent} extends Component {",
            " render() {",
            " return (",
            " <View style={styles.container}>",
            " <Text>I'm the ${1:NewComponent} component</Text>",
            " </View>",
            " )",
            " }",
            "} ",
            "const styles = StyleSheet.create({",
            " container: {",
            " flex: 1,",
            " },",
            "})"
        ],
        "description":"React Native Component File"
    },
    "React Native Pure Component File":{
        "prefix":"_rnp",
        "body":[
            "import React, { Component } from 'react'",
            "import {",
            " View,",
            " Text,",
            " StyleSheet,",
            "} from 'react-native' ",
            "const ${1:NewComponent} = ({}) => (",
            " <View style={container}>",
            " <Text>I'm ${1:NewComponent}</Text>",
            " </View>",
            ") ",
            "export default ${1:NewComponent} ",
            "const styles = StyleSheet.create({",
            " container: {",
            " flex: 1,",
            " },",
            "})"
        ],
        "description":"React Native Pure Component File"
    },
    "React: componentDidMount()":{
        "prefix":"_cdm",
        "body":[
            "componentDidMount() {",
            " $1",
            "}"
        ],
        "description":"React: componentDidMount() { ... }"
    },
    "React: componentDidUpdate(pp, ps)":{
        "prefix":"_cdup",
        "body":[
            "componentDidUpdate(prevProps, prevState) {",
            " $1",
            "}"
        ],
        "description":"React: componentDidUpdate(pp, ps) { ... }"
    },
    "React: constructor(props)":{
        "prefix":"_cns",
        "body":[
            "constructor(props) {",
            " super(props)",
            " this.state = {",
            " $1:",
            " }",
            "}"
        ],
        "description":"React: constructor(props) { ... }"
    },
    "React: componentWillMount()":{
        "prefix":"_cwm",
        "body":[
            "componentWillMount() {",
            " $1",
            "}"
        ],
        "description":"React: componentWillMount() { ... }"
    },
    "React: componentWillReceiveProps(np)":{
        "prefix":"_cwr",
        "body":[
            "componentWillReceiveProps(nextProps) {",
            " $1",
            "}"
        ],
        "description":"React: componentWillReceiveProps(np) { ... }"
    },
    "React: componentWillUpdate(np, ns)":{
        "prefix":"_cwup",
        "body":[
            "componentWillUpdate(nextProps, nextState) {",
            " $1",
            "}"
        ],
        "description":"React: componentWillUpdate(np, ns) { ... }"
    },
    "React: componentWillUnmount()":{
        "prefix":"_cwu",
        "body":[
            "componentWillUnmount() {",
            " $1",
            "}"
        ],
        "description":"React: componentWillUnmount() { ... }"
    },
    "React: shouldComponentUpdate(np, ns)":{
        "prefix":"_scu",
        "body":[
            "shouldComponentUpdate(nextProps, nextState) {",
            " $1",
            "}"
        ],
        "description":"React: shouldComponentUpdate(np, ns) { ... }"
    },
    "React: setState()":{
        "prefix":"_sst",
        "body":[
            "this.setState({",
            " $1:",
            "})"
        ],
        "description":"React: setState({ ... })"
    },
    "React: this.state.":{
        "prefix":"_state",
        "body":[
            "this.state$1"
        ],
        "description":"React: this.state."
    },
    "React: this.props.":{
        "prefix":"_props",
        "body":[
            "this.props$1"
        ],
        "description":"React: this.props."
    },
    "React: defaultProps":{
        "prefix":"_dp",
        "body":[
            "static defaultProps = {",
            " $1:",
            "}"
        ],
        "description":"React: defaultProps = { ... }"
    },
    "React: propTypes":{
        "prefix":"_pt",
        "body":[
            "static propTypes = {",
            " $1: PropTypes.string.isRequired",
            "}"
        ],
        "description":"React: propTypes = { ... }"
    },
    "React: ":{
        "prefix":"<View",
        "body":[
            "<View style={}>",
            " ",
            "</View>"
        ],
        "description":"React: <View>"
    },
    "React: ":{
        "prefix":"<ScrollView",
        "body":[
            "<ScrollView style={}>",
            " $1",
            "</ScrollView>"
        ],
        "description":"React: <ScrollView>"
    },
    "React: ":{
        "prefix":"<View/",
        "body":[
            "<View style={}/>"
        ],
        "description":"React: <View/>"
    },
    "React: ":{
        "prefix":"<Image",
        "body":[
            "<Image style={} source={}/>"
        ],
        "description":"React: <Image>"
    },
    "React: ":{
        "prefix":"<ListView",
        "body":[
            "<ListView",
            " contentContainerStyle={}",
            " dataSource={}",
            " renderRow={(rowData) => }",
            " enableEmptySections={true}/>"
        ],
        "description":"React: <ListView>"
    },
    "React: ListView.DataSource":{
        "prefix":"_lds",
        "body":[
            "const ${1:ds} = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2})"
        ],
        "description":"React: ListView.DataSource"
    },
    "React: ":{
        "prefix":"<Navigator",
        "body":[
            "<Navigator",
            " initialRoute={{",
            " component: ",
            " }}",
            " renderScene={(route, navigator) => {",
            " return <route.component {...route.params} navigator={navigator} />",
            " }}/>"
        ],
        "description":"React: <Navigator>"
    },
    "React: ":{
        "prefix":"<Text",
        "body":[
            "<Text style={}></Text>"
        ],
        "description":"React: <Text>"
    },
    "React: ":{
        "prefix":"<TextInput",
        "body":[
            "<TextInput",
            " style={}/>"
        ],
        "description":"React: <TextInput>"
    },
    "React: ":{
        "prefix":"<TouchableOpacity",
        "body":[
            "<TouchableOpacity",
            " style={}",
            " activeOpacity={}",
            " onPress={() => }>",
            " $1",
            "</TouchableOpacity>"
        ],
        "description":"React: <TouchableOpacity>"
    },
    "React: ":{
        "prefix":"<TouchableHighlight",
        "body":[
            "<TouchableOpacity",
            " style={}",
            " activeOpacity={}",
            " underlayColor=''",
            " onPress={() => }>",
            " $1",
            "</TouchableOpacity>"
        ],
        "description":"React: <TouchableHighlight>"
    },
    "React: ":{
        "prefix":"<TouchableWithoutFeedback",
        "body":[
            "<TouchableWithoutFeedback",
            " style={}",
            " onPress={() => }>",
            " $1",
            "</TouchableWithoutFeedback>"
        ],
        "description":"React: <TouchableWithoutFeedback>"
    },
    "React: Dimensions":{
        "prefix":"_dim",
        "body":[
            "const { width, height } = Dimensions.get('window')"
        ],
        "description":"React: Dimensions"
    },
    "React: Refs":{
        "prefix":"_ref",
        "body":[
            "this.refs['$1']."
        ],
        "description":"React: Refs"
    },
    "React: Import":{
        "prefix":"_imp",
        "body":[
            "import o from '$1'"
        ],
        "description":"React: Import"
    },
    "React: Log":{
        "prefix":"_log",
        "body":[
            "console.log($1)"
        ],
        "description":"React: Log"
    }
}
```



