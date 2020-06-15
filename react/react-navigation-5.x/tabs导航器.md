# tabs 导航器

安装

`yarn add @react-navigation/bottom-tabs`

或

`npm install @react-navigation/bottom-tabs`

## 最基本使用

```jsx
<NavigationContainer>
    <Tab.Navigator>
         <Tab.Screen name={"Home"} component={Home} />
         <Tab.Screen name={'Home2'} component={Home2} />
    </Tab.Navigator>
</NavigationContainer>
```

##  自定义外观

