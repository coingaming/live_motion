<h1><img src="https://github.com/benvp/live_motion/raw/main/static/images/logo.png" alt="🍿 LiveMotion" width="400"></h1>

LiveMotion enables high performance animations declared on the server and run on the client.

## Documentation

You can find the full API documentation on [hexdocs.pm/live_motion](https://hexdocs.pm/live_motion/LiveMotion.html).

Additionally you can find a few examples here: [LiveMotion Examples](https://livemotion.benvp.co).

## Features

- Define animations declaratively directly in your HEEX templates. No definition of CSS
  classes or keyframes required.
- Animate page transitions using mount/unmount animations. See a little example
  at [benvp.co](https://benvp.co). It's very subtle, but when you navigate, you see the page transitions.
- Trigger animations directly on the client using `LiveMotion.JS` (e.g. button clicks).

## Installation

LiveMotion makes use of Phoenix LiveView hooks and therefore the setup requires a few more steps than just adding the dependency to the Mixfile. However, the JS part is included and works without node installed.

Add the `live_motion` dependency to your `mix.exs` file.

```elixir
def deps do
  [
    {:live_motion, "~> 0.1.1"}
  ]
end
```

Open up your `app.js` and import `createLiveMotion` and initialize LiveMotion.

```js
import { createLiveMotion } from 'live_motion';

const { hook: motionHook, handleMotionUpdates } = createLiveMotion();
```

Now in your hook definition, add the LiveMotion hook.

```js
const hooks = {
  // your other hooks
  // ...
  ...motionHook,
};
```

Finally, we need to make sure that LiveView does not overwrite the style attributes generated by LiveMotion. In your `LiveSocket` initialization, add the following function into `onBeforeElUpdated(from, to)`.

```js
let liveSocket = new LiveSocket('/live', Socket, {
  params: { _csrf_token: csrfToken },
  hooks,
  dom: {
    onBeforeElUpdated(from, to) {
      // add this line
      handleMotionUpdates(from, to);
    },
  },
});
```

If you are using node and have npm or yarn installed, one additional step is required.

<details>
  <summary>Show Instructions for npm / yarn</summary>

We need to add the JS part as a dependency to our package.json file. Add this to your package.json
file.

```json
{
  "dependencies": {
    "live_motion": "file:../deps/live_motion"
  }
}
```

Don't forget to run `npm install` or `yarn` afterwards.
</details>

### Roadmap

There are still a lot of things to do to get to a mature library.

- Add tests
- Improve documentation
- See the limitations section.

### Limitations

LiveMotion is still in the early days, so breaking changes are expected. Additionally, there
are still a few limitations, which will be added in future releases.

- `LiveMotion.motion` always renders as a `div` element. There are plans on providing
  an api to support rendering any HTML element.
- Currently standard easing functions (e.g. `ease-in-out`, bezier curves) and spring
  animations are supported. Due to the nature of spring animations, they do not have
  a fixed duration (theoretically they can run indefinetely). When using unmount transition,
  this adds a challenge, as we do not know how long Phoenix LiveView should defer the actual
  removal of the DOM node. For now, we fall back to the maximum supported duration of ten seconds.
- Support Stagger and glide animations will be added in a later release.
- Layout animations are not yet supported. Think of animations which automatically move
  items in the correct place after an element is removed which affects the layout.

## Contributing

Any contribution is very much welcome. The project itself works just as any other mix project.

Please do not include an updated `priv/static/live_motion.js` in pull requests. The maintainers will update it as part of the release process.
