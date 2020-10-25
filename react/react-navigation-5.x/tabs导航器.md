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

```jsx
<NavigationContainer>
  <Tab.Navigator>
      <Tab.Screen name={"Home"} component={Home} options={{
          tabBarIcon: ({focused,color, size}) => {
              let iconName;
              iconName = focused ? 'ios-list-box' : 'ios-list'
              return <Ionicons name={iconName} size={size} color={color} />;
          }
      }}/>
      <Tab.Screen name={'Home2'} component={Home2} />
  </Tab.Navigator>
</NavigationContainer>
```

