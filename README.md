[![version](https://img.shields.io/crates/v/iced_drop.svg)](https://crates.io/crates/iced_drop)
[![build](https://github.com/pepa65/iced_drop/actions/workflows/ci.yml/badge.svg)](https://github.com/pepa65/iced_drop/actions/workflows/ci.yml)
[![dependencies](https://deps.rs/repo/github/pepa65/iced_drop/status.svg)](https://deps.rs/repo/github/pepa65/iced_drop)
[![docs](https://img.shields.io/badge/docs-iced__drop-blue.svg)](https://docs.rs/crate/iced_drop/latest)
[![license](https://img.shields.io/badge/License-MIT-blue.svg)](https://github.com/pepa65/iced_drop/blob/main/LICENSE)
[![downloads](https://img.shields.io/crates/d/iced_drop.svg)](https://crates.io/crates/iced_drop)

# iced_drop 0.1.42

**Small library providing a custom widget and operation to implement drag and drop in [iced](https://github.com/iced-rs/iced/tree/master)**

## Usage
To add drag and drog functionality, first define two messages with the following format:
```rust
enum Message {
	Drop(iced::Point, iced::Rectangle)
	HandleZones(Vec<(iced::advanced::widget::Id, iced::Rectangle)>)
}
```

The `Drop` message will be sent when the droppable is being dragged, and the left mouse button is released. This message provides the mouse position and layout boundaries of the droppable at the release point.

The `HandleZones` message will be sent after an operation that finds the drop zones under the mouse position. It provides the Id and bounds for each drop zone.

Next, create create a droppable in the view method and assign the on_drop message. The dropopable function takes an `impl Into<Element>` object, so it's easy to make a droppable from any iced widget.

```rust
iced_drop::droppable("Drop me!").on_drop(Message::Drop);
```

Next, create a "drop zone." A drop zone is any widget that operates like a container andhas some assigned Id. It's important that the widget is assigned some Id or it won't be recognized as a drop zone.

```rust
iced::widget::container("Drop zone")
	.id(iced::widget::container::Id::new("drop_zone"));
```

Finally, handle the updates of the drop messages

```rust
match message {
	Message::Drop(cursor_pos, _) => {
		return iced_drop::zones_on_point(
			Message::HandleZonesFound,
			point,
			None,
			None,
		);
	}
	Message::HandleZones(zones) => {
		println!("{:?}", zones)
	}
}
```

On Drop, we return a widget operation that looks for drop zones under the cursor_pos. When this operation finishes, it returns the zones found and sends the `HandleZones` message. In this example, we only defined one zone, so the zones vector will either be empty if the droppable was not dropped on the zone, or it will contain the `drop_zone`

## Future Development
Right now it's a little annoying having to work with iced's Id type. At some point, I will work on a drop_zone widget that can take some generic clonable type as an id, and I will create a seperate find_zones operation that will return a list of this custom Id. This should make it easier to determine which drop zones were found.
