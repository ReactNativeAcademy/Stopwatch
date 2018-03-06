Application Design
==================

Hello Everyone and welcome to the React Native Academy! In this tutorial we will build the iOS Stopwatch application in React Native.

The app consists of three screens.

On the initial screen you can see the timer set to zeroes and two buttons: the lap button and the start button. The lap button is disabled until you start the timer with the Start button.

Once it is running, I can press the lap button to measure the split times. When there are at least three laps, The slowest and the fastest intervals are highlighted. The current lap increases together with the main timer.

When the stopwatch is running, you can stop it. From here you can resume or reset. Reset brings you back to the initial screen.

We will refer to these ddesigns during development to pick the colors and make sure that our app resembles the original iOS Stopwatch application.

Discuss the components
======================

Before we start the implementation, let’s make a quick “Thinking in React” excersise. When you start a development project, usually you get some kind of mockups or designs. The first step is to break them down into the components that might be needed in the application.

The best way to identify the components is to think about their responsibility - each component should ideally do one thing. If it does more, it should be split into more specific components.

You can also take a look at the data displayed in the app. Each component should present a single piece of data.

I mark the components with rectangles. Let’s give them some meanigful names and discuss their responsibility.

App, the top component, houses all the other components.

Timer displays current time measurement.

ButtonsRow display individual Buttons arranged horizontally.

RoundButton is this circle button component.

Next we’ve got LapsTable - which is a scrollable list of individual Laps.

Each Lap component displays the measurement of a single interval.

1\. Create React Native App
===========================

We’ve got the components identified, so let’s start the development by creating a project with the Create React Native App tool.

```sh
create-reacte-native-app StopwatchApp
```

I enter the project folder and start the packager:

```sh
cd StopwatchApp
yarn start
```

The packager bundles all the source file together. It builds the app and starts the development server.

2\. Data Model
==============

Now as the app is running and the componentes identified let’s think about the data model that represents the application data. It will be used to develop a static version of the application that renders the user interface. 

We need a minimal data model at this moment, it will probably evolve over time and go into components state. But now for simplicity we will store is as a constant inside App.js.

```js
const DATA = {
  timer: 1234567,
  laps: [ 12345, 23456, 34567, 98765],
}
```

We have the main timer. It displays duration of time. We will store it as a number of milliseconds between starting the timer and now.

Next there is a list of laps. We will store them as an array in the very same format - as milliseconds representing invervals.

3\. Static version and App
==========================

Let’s develop the app now. We will start a static application that renders the user interface based on the data we defined in the previous step. Our app should take the data model and just display it.

I start by removing all the semicolons. They are totally optional in JavaScript and for me not using them makes the code more clear and readable. This is a matter of personal preference so just make a choice and stay consistent.

Next I remove the contents of the App component and import the Component directly to have simpler extends form.

Now I modify the style of the App component. Styles in React Native are written with JavaScript. The syntax is similar to CSS but there are some differences - for instance the styles are not inherited by child components.

I remove justifyContent as I don’t want the content to be centered vertically.

4\. Timer
=========

It’s time to implement the Timer component. It will be a functional component that takes a single property - the timer reading.

```js
function Timer({ interval }) {
  return <Text>{interval}</Text>
}
```

Let’s use it inside the App and pass the data from our data model:

```js
<Timer interval={DATA.timer}/>
```

You can see that nothing has changed because the default text color in React Native in black. Let’s add some styling to the Timer:

```js
  return <Text style={styles.timer}>{interval}</Text>
```

I change the color to white and make the text bigger. Let’s make it also thinner.

```js
  timer: {
    color: '#FFFFFF',
    fontSize: 76,
    fontWeight: '200',
  },
```

I will add some space above the timer by setting top padding property of the App component container style:

```js
    paddingTop: 130,
```

I could either set the padding inside the App or top margin of the Timer. It’s better when the parrent component arrenges its children. This way we might use the Time in other places of the application.

The timer looks good, but it renders the interval just as a number of milliseconds. We need to parse it into minutes, seconds and fractions of the second. I will add moment library for that. Moment is the most popular JavaScript library to parse and manipulate dates:

```sh
yarn add moment
```

I import it:

```sh
import moment from 'moment'
```

…and use it to parse the milliseconds:

```sh
function Timer({ interval }) {
  const duration = moment.duration(interval)
  return (
    <Text style={styles.timer}>
      {duration.minutes()}:{duration.seconds()},{duration.milliseconds()}
    </Text>
  )
}
```

Looks good, but I only want two decimal places after number of seconds. I devide number of milliseconds by 10 and then I need to round it:

```sh
function Timer({ interval }) {
  const duration = moment.duration(interval)
  const centiseconds = Math.floor(duration.milliseconds() / 10)
  return (
    <Text style={styles.timer}>
      {duration.minutes()}:{duration.seconds()},{centiseconds}
    </Text>
  )
}
```

Now the Timer component is done!

5\. RoundButton
===============

In this step we will display a row with two buttons. Let’s start with a single button. It will be implemented as a RoundButton functional component.

```js
function RoundButton() {
  return (
    <View>

    </View>
  )
}
```

It has three properties: a title, a text color and a background color.

```js
function RoundButton({ title, color, background }) {
```

Title is displayed as a Text component. Color is passed as in inline style to it. Background is passed to the View.

```js
<View style={{ backgroundColor: background }}>
  <Text style={{ color }}>{title}</Text>
</View>
```

Let’s display the button below the Timer. I pick the colors from the Start button.

```js
<RoundButton color='#53D86A' background='#1B361F' title='Start'/>
```

Let’s ass some basic styling to make the button. I place it in square brackets to make it work together with the background color I set inline.

```js
<View style={[styles.button, { backgroundColor: background }]}>
```

Now I define generic round button style. Let’s make it 80 by 80\. To make it round I set border radius to 40 - half of its size. I need to center the title.

```js
button: {
  width: 80,
  height: 80,
  borderRadius: 40,
  alignItems: 'center',
  justifyContent: 'center',
}
```

Let’s make the text bigger.

\<Text style={[styles.buttonTitle, { color }]}\>{title}\</Text\>

and then

buttonTitle: {

 fontSize: 18,

 }

Now I will implement the thin border inside the button. It will be another View component inside the button.

```js
<View style={[styles.button, { backgroundColor: background }]}>
  <View style={styles.buttonBorder}>
    <Text style={[styles.buttonTitle, { color }]}>{title}</Text>
  </View>
</View>
```

Let’s style it. I make it just a bit smaller than the whole button - 76 by 76\. Border radius is half of the size. I set tbe border color to the same value as the background color of the whole app and border width as 2.

I also need to center the title inside this view.

```js
<View style={styles.buttonBorder}>
...
  buttonBorder: {
    width: 76,
    height: 76,
    borderRadius: 38,
    borderColor: '#0D0D0D',
    borderWidth: 2,
    alignItems: 'center',
    justifyContent: 'center',
  },
```

The basic button component is ready. 

6\. ButtonsRow
==============

Let’s try to display the Reset button left to the Start button just like on the screen. I pick the colors from the screen again:

```js
<RoundButton color='#FFFFFF' background='#3D3D3D' title='Reset'/>
```

The buttons are displayed one below the other. I need to put them inside their own container component that will arrange the buttons horizontaly. I call it ButtonsRow. Its row is to arrange its children horizontally.

```js
function ButtonsRow({ children }) {
  return (
    <View style={styles.buttonsRow}>
      {children}
    </View>
  )
}
```

I will surround Buttons with it now.

```js
<ButtonsRow>
  <RoundButton color='#53D86A' background='#1B361F' title='Start'/>
  <RoundButton color='#FFFFFF' background='#3D3D3D' title='Reset'/>
</ButtonsRow>
```

Now let’s define buttonsRow style. I set flexDirection to row and set justifyContent to space-between so that the buttons are spread to the sides of the screen. I also need to set alignSelf to stretch so that the ButtonsRow component takes whole available space. I add some space above the buttons by setting the top margin.

buttonsRow: {

 flexDirection: 'row',

 alignSelf: 'stretch',

 justifyContent: 'space-between’,

 marginTop: 80,

 },

The final thing to do is to add the horizontal padding to the App component so that there is some space around the buttons.

```js
  container: {
    ...
    paddingHorizontal: 20,
  },
```

7\. LapsTable
=============

In the next step I will display the laps. Let’s define a components for laps table and for an individual lap.

I start with the Lap. It takes two properties - lap number and its duration.

```js
function Lap({ number, interval }) {
  return (
    <View>
      <Text>Lap {number}</Text>
      <Text>{interval}</Text>
    </View>
  )
}
```

Next goes the LapsTable component. I start by wrapping the contents with the ScrollView so that it will be possible to browse the laps f they don’t fit the screen. I map elements inside the laps array into Lap elements. Interval is lap duration in milliseconds.

I assume that the current lap is at position 0 while the first lap is at the very end of the laps array. It will make displaying the laps in the descending order easier. I calculate the lap number by subtracting lap index inside the laps array from the total number of laps. The number identifies the lap uniquely so I use it as a key.

```js
import { StyleSheet, Text, View, ScrollView } from 'react-native'
...
function LapsTable({ laps }) {
  return (
    <ScrollView>
      {laps.map((lap, index) => (
        <Lap
          interval={lap}
          number={laps.length - index}
          key={laps.length - index}
        />
      ))}
    </ScrollView>
  )
}
```

Let’s style the LapsTable and Lap components now and add basic style:

```js
  lap: {
    flexDirection: 'row',
    justifyContent: 'space-between',
  },
  lapText: {
    color: '#FFFFFF',
    fontSize: 18,
  }
```

The number and duration are crunched. I need to stretch the ScrollView acroll the screen.

```js
<ScrollView style={styles.scrollView}>
...
  scrollView: {
    alignSelf: 'stretch',
  },
```

Let’s add the border between the elements. Is it enough to add the top border. I pick its color from our screenshots.The laps are confined. I will add vertical padding.

```js
  lap: {
    borderTopWidth: 1,
    borderColor: '#171717',
    paddingVertical: 10,
```

Let’s also add some space below the buttons.

```js
  buttonsRow: {
    ...
    marginBottom: 20,
  },
```

We have two more things to do with implementing the laps: parse the interval duration and highlighting the fastest and the slowest lap.

For laps duration we could reuse the Timer component. It does exaclty the same, only has different style. Lets make the style a parameter of the Timer component:

```js
function Timer({ interval, style = { }}) {
  const duration = moment.duration(interval)
  const centiseconds = Math.floor(duration.milliseconds() / 10)
  return (
    <Text style={style}>
```

Now I can use the Timer directly inside the Lap component. Style remains unchanged - I still want to pass it to the timer. The only difference is that interval is now passed as a property:

```js
      <Text style={styles.lapText}>Lap {number}</Text>
      <Timer style={styles.lapText} interval={interval}/>
```

To highlight the fastest and the slowest lap, I will add two new properties to the lap component: fastest and slowest:

```js
function Lap({ number, interval, fastest, slowest }) {
```

I will pick the colors from the design and create correposnding styles.

```css
  fastest: {
    color: '#90D970',
  },
  slowest: {
    color: '#D04F38',
  },
```

They will be aplpied conditionally if the corresponding property is true. I exctract lap style into a constant and apply the logic. The Lap component is ready now.

```js
function Lap({ number, interval, fastest, slowest }) {
  const lapStyle = [
    styles.lapText,
    fastest && styles.fastest,
    slowest && styles.slowest,
  ]
  return (
    <View style={styles.lap}>
      <Text style={lapStyle}>Lap {number}</Text>
      <Timer style={lapStyle} interval={interval}/>
    </View>
  )
}
```

Now I ill find these laps inside LapsTable component. I start by extracting all the lap but the first one. The slice method returns a copy of the original laps array, starting from the first element to the end, without modifying the original laps array.

```js
  const finishedLaps = laps.slice(1)
```

I set the initial values for min and max. Min is set to the max Integer number in JavaScript to ensure that at least one min lap will be find. Max is set to the minimum integer number.

```js
  let min = Number.MAX_SAFE_INTEGER
  let max = Number.MIN_SAFE_INTEGER
```

If there are at least two finished laps I will fo through them and pick the fastest and the slowest one.

```js
  if (finishedLaps.length >= 2) {
    finishedLaps.forEach(lap => {
      if (lap < min) min = lap
      if (lap > max) max = lap
    })
  }
```

8\. Design State
================

We are ready now to handle user actions now. The first step will be to design UI state. In React we use component state to control the data that changes over time. It is important to identify which components owns that data. Usually it is the top level component that passes the data to its children components as props. In our case data will owned be the App component.

I start by migrating the data model into App component state. The initial state values are defined inside component constructor.

```js
  constructor(props) {
    super(props)
    this.state = {
      timer: 1234567,
      laps: [ 12345, 23456, 34567, 98765],
    }
  }
```

What should happen then the Start button is pressed? The stopwatch start measuring the time. To calculate the duration I neede to store the time when the measuring it starts. I add the start property to the state and assume initial value of 0\. It will represent the stopwatch not measuing the time in the very moment.

To calculate the duration I also need the current time. Let’s call it now. I put it into state to triggerr the app re-render when the value updates. When I substract start from now I get the timer duration. I don’t need a separate timer property. I also make the laps array empty as we have no laps when the app starts.

```js
  constructor(props) {
    super(props)
    this.state = {
      laps: [ ],
      start: 0,
      now: 0,
    }
  }
```

start and now will be represented as a number of milliseconds since the Empoch time, which is January 1st 1970\. This is what getTime method of the Date object returns in JavaScript .

9\. Start
=========

Now when the state is ready handling the button actions is easy.

I definede the start class field - it represents the handler function. I pass it as the property to the Start button. I also and the Lap button and comment out the Reset button - we don’t need it now. The Lap button should be disabled when the timer is not running. I don’t want it to handle any events or change opacity when pressed. This will be handled by the disabled property.

```js
  start = () => {
  
  }
  ...
  <RoundButton color='#FFFFFF' background='#3D3D3D' title='Reset'/>
  <RoundButton color='#53D86A' background='#1B361F' title='Start' onPress={this.start}/>
```

I use TouchableOpacity to render the RoundButton. I add onPress and disabled properties to the Round Button. onPress is passed to the TouchableOpacity, which can handle it.

I also increase the default initial opacity. The final step is to handle disabled property.

If a button is disabled, it’s opacity is always 1\. If this is the case, onPress callback should not be executed.

```js
function RoundButton({ title, color, background, onPress, disabled }) {
  return (
    <TouchableOpacity
      style={[styles.button, { backgroundColor: background }]}
      onPress={() => !disabled && onPress()}
      activeOpacity={disabled ? 1 : 0.7}
    >
      <View style={styles.buttonBorder}>
        <Text style={[styles.buttonTitle, { color }]}>{title}</Text>
      </View>
    </TouchableOpacity>
  )
}
```

Let’s get back to the start method. I set the current timestamp and set state.start and state.now with it and create a single empty lap:

const now = new Date().getTime()

 this.setState({

 laps: [0],

 start: now,

 now,

 })

Still nothing really happens as I the timer should be updated on a continuous basis. I will use setInterval function to update state.now property:

```js
    this.timer = setInterval(() => { 
      this.setState({
        now: new Date().getTime()
      })
    }, 100)
```

Let’s make the lap updated. I will add the current timer to the first lap. First I create a constant for timer interval and pass it to the LapsTable and the Timer.

```js
const timer = now - start
...
<Timer interval={timer} style={styles.timer}/>
...
<LapsTable laps={laps} timer={timer}/>
```

I update the LapsTable:

```js
function LapsTable({ laps, timer }) {
...
interval={index === 0 ? timer + lap : lap}
```

You can notice that the timer jumps left and right. This is becase the font that we use is not monospaces - digits have different width. Also if any of the timer parts is less than 10 we should add a leading zero. Let’s defined the pad function:

```js
const pad = n => n < 10 ? '0' + n : n
```

To fix the Timer I will wrap minutes, seconds and centiseconds with their own Text components with fixed width. The whole timer is placed inside a View that has flexDirection set to row. Now the style is applied to each element.

```js
    <View style={styles.timerContainer}>
      <Text style={style}>{pad(duration.minutes())}:</Text>
      <Text style={style}>{pad(duration.seconds())},</Text>
      <Text style={style}>{pad(centiseconds)}</Text>
    </View>
    ...
  timerContainer: {
    flexDirection: 'row',
  },
```

The last thing to do is to set the width for the lap timer the same as we did for the main timer:

```js
<Timer style={[styles.lapPart, lapStyle]} interval={interval}/>
...
lapPart: {
    width: 28,
  },
```

9\. Lap & Stop
==============

When the timer is running, there is a different set of buttons rendered. Let’s create a ButtonRow for them. The Lap button looks just like the Reset button, so I reuse it. For the Stop button I pick the colors from the designs.

```js
        <ButtonsRow>
          <RoundButton color='#FFFFFF' background='#3D3D3D' title='Lap' onPress={this.lap}/>
          <RoundButton color='#CE4F39' background='#341915' title='Stop' onPress={this.stop}/>
        </ButtonsRow>
```

The first row should be rendered only when the app starts and no button has been pressed. It happens only when the laps array is emty. I will render this row conditionally:

```js
{laps.length === 0 && (
...
)}
```

The Lap and Stop buttons are present when the timer is running - that is state.start property is \> 0

```js
{start > 0 && (
```

Now let’s implement the handler methods. When the lap is pressed I insert a new lap at the first position of the state.laps array:

lap = () =\> {

 const { laps } = this.state

 this.setState({

 laps: [0, ...laps]

 })

 }

This does not save current split time. I should first save the current timer value into the laps array. I also need to reset start and now with current timestamp value so that the new lap counts from 0.

```js
  lap = () => {
    const timestamp = new Date().getTime()
    const { laps, now, start } = this.state
    const [firstLap, ...other] = laps
    this.setState({
      laps: [0, firstLap + now - start, ...other],
      start: timestamp,
      now: timestamp,
    })
  }
```

Looks good with the only exception that the main timer alco counts from 0 again. I shall add the duration of previous laps to have the total time displayed.

\<Timer interval={laps.reduce((total, curr) =\> total + curr, 0) + timer} style={styles.timer}/\>

The stop method is similar to the lap. I will copy it. First I clear the interval so that it no longer runs.

When stop is pressed I want to freeze the timer. I can do it be setting state now and start proeprties to 0s. I also don’t add a new lap. Instead I add the value of the current timer reading to the first tap.

Each time stop is pressed, start and now are zeroed and current lap is updated.

```js
  stop = () => {
    clearInterval(this.timer)
    const { laps, now, start } = this.state
    const [firstLap, ...other] = laps
    this.setState({
      laps: [firstLap + now - start, ...other],
      start: 0,
      now: 0,
    })
  }
```

11\. Reset & Resume
===================

Rest and Resume buttons are displayed in situation, when we’ve got some laps in the state.lap array but the timer is stopped.

```js
        {laps.length > 0 && start === 0 && (
          <ButtonsRow>
          <RoundButton color='#FFFFFF' background='#3D3D3D' title='Reset' onPress={this.reset}/>
            <RoundButton color='#53D86A' background='#1B361F' title='Start' onPress={this.resume}/>
          </ButtonsRow>
        )}
```

Reset clears the state to its initial value

```js
  reset = () => {
    this.setState({
      laps: [],
      started: 0,
      now: 0,
    })
  }
```

Resume sets state and now to current timestamp and starts the timer again.

```js
  resume = () => {
    const now = new Date().getTime()
    this.setState({
      start: now,
      now,
    })
    this.timer = setInterval(() => { 
      this.setState({
        now: new Date().getTime()
      })
    }, 100)
  }
```

The last thing to do is to clear the timer when the App component unmounts so that all allocated resources are released.

componentWillUnmount() {

 this.clearInterval(this.timer)

 }

12\. Android
============

The app can be easily run on Android.

13\. Summary
============

Well done - if you followed me along, you’ve got a stopwatch app running now! 

Lets wrap up everything. We started with the static designs and broke them down into static omponents. Based on that we implemented the user interface of the app. Next we designed the application state and handled interactions.

That’s quite a lot, but there is so much more to learn about React Native. We used only the most basic components, didn’t use any animations and there is no navigation since the whole app is a single screen. When you work on a real app you also implement autoamted tests and use continuous integration to run them. We haven’t touch app releases and updated.

If you want to learn all of the above and get a small group React Native coaching join the React Native Academy at reactnative.education.

