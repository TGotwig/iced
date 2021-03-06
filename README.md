# Iced
[![Build Status](https://travis-ci.org/hecrj/iced.svg?branch=master)](https://travis-ci.org/hecrj/iced)
[![Documentation](https://docs.rs/iced/badge.svg)][documentation]
[![Crates.io](https://img.shields.io/crates/v/iced.svg)](https://crates.io/crates/iced)
[![License](https://img.shields.io/crates/l/iced.svg)](https://github.com/hecrj/iced/blob/master/LICENSE)

A renderer-agnostic GUI library for Rust focused on simplicity and type-safety.
Inspired by [Elm].

[![Tour - Iced][gui_gif]][gui_gfycat]
[![Tour - Coffee][coffee_gui_gif]][coffee_gui_gfycat]

[gui_gif]: https://thumbs.gfycat.com/VeneratedSourAurochs-small.gif
[gui_gfycat]: https://gfycat.com/veneratedsouraurochs

[coffee_gui_gif]: https://thumbs.gfycat.com/GloomyWeakHammerheadshark-small.gif
[coffee_gui_gfycat]: https://gfycat.com/gloomyweakhammerheadshark

## Features
  * Simple, easy-to-use, renderer-agnostic API
  * Responsive, flexbox-based layouting
  * Type-safe, reactive programming model
  * Built-in widgets
  * Custom widget support

__Iced is in a experimental stage.__ [Check out the issues] and
[feel free to contribute!].

[check out the issues]: https://github.com/hecrj/iced/issues
[feel free to contribute!]: #contributing--feedback

## Installation
Add `iced` as a dependency in your `Cargo.toml`:

```toml
iced = "0.1"
```

__Iced moves fast and the `master` branch can contain breaking changes!__ If
you want to learn about a specific release, check out [the release list].

[the release list]: https://github.com/hecrj/iced/releases

## Overview
Inspired by [The Elm Architecture], Iced expects you to split user interfaces
into four different concepts:

  * __State__ — the state of your application
  * __Messages__ — user interactions or meaningful events that you care
  about
  * __View logic__ — a way to display your __state__ as widgets that
  may produce __messages__ on user interaction
  * __Update logic__ — a way to react to __messages__ and update your
  __state__

We can build something to see how this works! Let's say we want a simple counter
that can be incremented and decremented using two buttons.

We start by modelling the __state__ of our application:

```rust
use iced::button;

struct Counter {
    // The counter value
    value: i32,

    // The local state of the two buttons
    increment_button: button::State,
    decrement_button: button::State,
}
```

Next, we need to define the possible user interactions of our counter:
the button presses. These interactions are our __messages__:

```rust
#[derive(Debug, Clone, Copy)]
pub enum Message {
    IncrementPressed,
    DecrementPressed,
}
```

Now, let's show the actual counter by putting it all together in our
__view logic__:

```rust
use iced::{Button, Column, Text};
use iced_wgpu::Renderer; // Iced does not include a renderer! We need to bring our own!

impl Counter {
    pub fn view(&mut self) -> Column<Message, Renderer> {
        // We use a column: a simple vertical layout
        Column::new()
            .push(
                // The increment button. We tell it to produce an
                // `IncrementPressed` message when pressed
                Button::new(&mut self.increment_button, "+")
                    .on_press(Message::IncrementPressed),
            )
            .push(
                // We show the value of the counter here
                Text::new(&self.value.to_string()).size(50),
            )
            .push(
                // The decrement button. We tell it to produce a
                // `DecrementPressed` message when pressed
                Button::new(&mut self.decrement_button, "-")
                    .on_press(Message::DecrementPressed),
            )
    }
}
```

Finally, we need to be able to react to any produced __messages__ and change our
__state__ accordingly in our __update logic__:

```rust
impl Counter {
    // ...

    pub fn update(&mut self, message: Message) {
        match message {
            Message::IncrementPressed => {
                self.value += 1;
            }
            Message::DecrementPressed => {
                self.value -= 1;
            }
        }
    }
}
```

And that's everything! We just wrote a whole user interface. Iced is now able
to:

  1. Take the result of our __view logic__ and layout its widgets.
  1. Process events from our system and produce __messages__ for our
     __update logic__.
  1. Draw the resulting user interface using our chosen __renderer__.

Browse the [documentation] and the [examples] to learn more!

## Implementation details
Iced was originally born as an attempt at bringing the simplicity of [Elm] and
[The Elm Architecture] into [Coffee], a 2D game engine I am working on.

The core of the library was implemented during May in [this pull request], using
[`stretch`] for flexbox-based layouting. It was later released as the main
feature of [Coffee 0.3.0].

After release, different folks suggested me to split the new [`ui` module] into
its own standalone crate, as it could potentially benefit other engines and
applications. I thought it was a great idea, and after a bit of work... Iced is
here!

As an interesting note, Iced does not rely on reference counting and interior
mutability. There is not a single `Rc`, `RefCell`, or similar used
directly in the library. As a consequence, compiler guarantees stay intact and
many kinds of pesky bugs and runtime errors are banished. No spooky action at
a distance!

[this pull request]: https://github.com/hecrj/coffee/pull/35
[`stretch`]: https://github.com/vislyhq/stretch
[Coffee 0.3.0]: https://github.com/hecrj/coffee/releases/tag/0.3.0
[`ui` module]: https://docs.rs/coffee/0.3.2/coffee/ui/index.html

## Contributing / Feedback
If you want to contribute, you are more than welcome to be a part of the
project! Check out the current [issues] if you want to find something to work
on. Try to share you thoughts first! Feel free to open a new issue if you want
to discuss new ideas.

Any kind of feedback is welcome! You can open an issue or, if you want to talk,
you can find me (and a bunch of awesome folks) over the `#games-and-graphics`
and `#gui-and-ui` channels in the [Rust Community Discord]. I go by
`@lone_scientist` there.

[documentation]: https://docs.rs/iced
[examples]: https://github.com/hecrj/iced/tree/master/examples
[Coffee]: https://github.com/hecrj/coffee
[Elm]: https://elm-lang.org/
[The Elm Architecture]: https://guide.elm-lang.org/architecture/
[issues]: https://github.com/hecrj/iced/issues
[Rust Community Discord]: https://bit.ly/rust-community
