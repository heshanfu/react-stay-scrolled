# react-stay-scrolled

[![Travis][build-badge]][build]
[![npm package][npm-badge]][npm]
[![Dependency Status][dependency-status-badge]][dependency-status]
[![devDependency Status][dev-dependency-status-badge]][dev-dependency-status]

> Keep your component, such as message boxes, scrolled down

## Live demo

You can see the simplest demo here: [Live demo](https://perrin4869.github.io/react-stay-scrolled)

## Install

```
$ npm install --save react-stay-scrolled
```

## Usage

`react-stay-scrolled` injects methods `stayScrolled` and `scrollBottom` to its children through the `scrolled` higher order component:

```javascript
// messages.jsx
import React, { Component, PropTypes } from 'react';
import StayScrolled from 'react-stay-scrolled';

class Messages extends Component {

  state = {
    notifyNewMessage: false
  }

  onStayScrolled = (isScrolled) => {
    // Tell the user to scroll down to see the newest messages if the element wasn't scrolled down
    this.setState({ notifyNewMessage: !isScrolled });
  }

  onScrolled = () => {
    // The element just scrolled down - remove new messages notification, if any
    this.setState({ notifyNewMessage: false });
  }

  render() {
    const { messages } = this.props;
    const { notifyNewMessage } = this.state;

    return (
      <div>
        <StayScrolled
          component="div"
          onStayScrolled={this.onStayScrolled}
          onScrolled={this.onScrolled}>
          {
            messages.map(
              (message, i) => <Message key={i} text={message} />
            )
          }
        </StayScrolled>
        { notifyNewMessage && <div>Scroll down to new message</div> }
      </div>
    );
  }

}
```

```javascript
// message.jsx
import React, { Component, propTypes } from 'react';
import { scrolled } from 'react-stay-scrolled';

class Message extends Component {

  static propTypes = {
    stayScrolled: PropTypes.func,
    scrollDown: PropTypes.func,
  }

  componentDidMount() {
    const { stayScrolled, scrollDown } = this.props;

    // Make the parent StayScrolled component scroll down if it was already scrolled
    stayScrolled();

    // Make the parent StayScrolled component scroll down, even if not completely scrolled down
    // scrollDown();
  }

  render() {
    return (<div>{this.props.text}</div>);
  }

}

export default scrolled(Message);
```

The methods can also be called from the parent element:

```javascript
import React, { Component, PropTypes } from 'react';
import StayScrolled from 'react-stay-scrolled';
import Velocity from 'velocity';

class Messages extends Component {

  componentDidUpdate(prevProps) {
    if(prevProps.messages.length < this.props.messages.length)
      this.stayScrolled(); // Or: this.scrollDown
  }

  storeScrolledControllers = ({ stayScrolled, scrollBottom }) => {
    this.stayScrolled = stayScrolled;
    this.scrollBottom = scrollBottom;
  }

  render() {
    const { messages } = this.props;

    return (
      <StayScrolled Velocity={Velocity} provideControllers={this.storeScrolledControllers}>
        {
          messages.map(
            (message, i) => <Message key={i} text={message} />
          )
        }
      </StayScrolled>
    );
  }

}
```

## Props

### component

Type: a React `component`, default: `"div"`. Passed to `React.createElement`, used to wrap the children

### debug

Type: a function, used to log debug messages in StayScrolled, usually `(msg) => { console.log(msg); }`

### stayAccuracy

Type: number, default: 0, defines an error margin, in pixels, under which `stayScrolled` will still scroll to the bottom

### provideControllers({ stayScrolled, scrollBottom })

Type: a function, used for getting scroll controllers to the parent elements, see the controller API below

### triggerStayScrolled

Type: any, upon modification, a stay scrolled is triggered, without triggering an event notification

### triggerStayScrolledNotify

Type: any, upon modification, a stay scrolled is triggered, triggering an event notification

### triggerScrollBottom

Type: any, upon modification, a scroll bottom is triggered

### Velocity

Type: `function` optional, default: `null`

If passed, scroll will be performed with a smooth animation powered by the Velocity instance passed

### onStayScrolled(scrolled)

Type: function, fires after executing `stayScrolled`, notifies back whether or not the component is scrolled down. Useful to know if you need to notify the user about new messages

### onScrolled()

Type: function, fires when the element scrolls down, useful to remove the new message notification

## Controllers

Two methods used for controlling scroll behavior.
Can be accessed by children by injecting into props with `scrolled` higher order component, or via context.
Can be accessed by parents by passing `provideControllers` prop to `StayScrolled`.

### stayScrolled(notify = true)

Scrolls down the element if it was already scrolled down - useful for when a user is reading previous messages, and you don't want to interrupt

#### notify

Type: `boolean` optional, default `true`. If `true`, it fires an `onStayScrolled` event after execution, notifying whether or not the component stayed scrolled

### scrollDown()

Scrolls down the wrapper element, regardless of current position

## Higher order component

### scrolled

Injects `stayScrolled` and `scrollBottom` to the props of a child element of `StayScrolled`

## TODO

* Try to automate scrolling on some of the use-cases
* Support jQuery for animation
* Improve examples
* Improve documentation with tables, etc
* Test

## License

See the [LICENSE](LICENSE.md) file for license rights and limitations (MIT).

[build-badge]: https://img.shields.io/travis/perrin4869/react-stay-scrolled/master.svg?style=flat-square
[build]: https://travis-ci.org/perrin4869/react-stay-scrolled

[npm-badge]: https://img.shields.io/npm/v/react-stay-scrolled.svg?style=flat-square
[npm]: https://www.npmjs.org/package/react-stay-scrolled

[dependency-status-badge]: https://david-dm.org/perrin4869/react-stay-scrolled.svg?style=flat
[dependency-status]: https://david-dm.org/perrin4869/react-stay-scrolled

[dev-dependency-status-badge]: https://david-dm.org/perrin4869/react-stay-scrolled/dev-status.svg?style=flat
[dev-dependency-status]: https://david-dm.org/perrin4869/react-stay-scrolled#info=devDependencies
