# microPixel driver

This library provides a driver for various micro Pixel LED products from Proto-PIC, 

microPixels consist of a number of RGB LEDs, every one of them controlled separately.  

## Basic usage

```blocks
// Create a microPixel driver - specify the pin, number of LEDs, and the type of 
// the microPixel srip, either standard RGB (with GRB or RGB format) or RGB+White.
let strip = micropixel.create(DigitalPin.P0, 24, microPixelMode.RGB)

// set pixel colors
strip.setPixelColor(0, 255, 255, 255) // white
strip.setPixelColor(1, 255, 0, 0)     // red
strip.setPixelColor(2, 0, 255, 0)     // green
strip.setPixelColor(3, 0, 0, 255)     // blue

// send the data to the strip
strip.show()
```

Use `strip.setBrigthness()` to lower the brightness (it's maxed out by default to 32), we have updated this to allow a max brightness of 32 - this limits the draw on the micro bit voltage regulator.

Use `strip.shift()` or `strip.rotate()` to shift the lights around.

Use `strip.setPixelWhiteLED()` to set brightness of the white pixel for RGB+W strips. 

## HSL color format

Use `micropixel.hsl()` to create colors using hue, saturation, and lightness.

Use `.toRGB()` on an HSL color to convert it into RGB format. All colors passed to `strip.setPixelColor()`
or `strip.showColor` must be in RGB format.

For performance reasons, it is best to avoid making too many `.toRGB()` calls per second. For color 
animations, this can be avoided by only computing the set of colors in the animation once. See the HSL example below. 

## Example: Using accelerometer to control colors

This little program will let the position of the microbit control the color of the first LED.
This first LED will then get shifted further away every 100ms.

```blocks
let strip = micropixel.create(DigitalPin.P0, 24)
while (true) {
    let x = input.acceleration(Dimension.X) / 2
    let y = input.acceleration(Dimension.Y) / 2
    let z = input.acceleration(Dimension.Z) / 2
    strip.setPixelColor(0, x, y, -z);
    strip.shift(1);
    strip.show();
    basic.pause(100);
}
```

## Example: Using HSL to produce a rainbow animation

This program uses HSL colors to display an animated rainbow pattern along the first 12 microPixels. 

```typescript
// setup
let stripLen = 12;
let np = micropixel.create(DigitalPin.P0, stripLen, microPixelMode.RGB)
np.setBrigthness(10)

// compute colors in HSL, store colors in RGB
let colors: number[] = [];
let hStep = 360 / stripLen;
for (let i = 0; i < stripLen; i++) {
    let h = i * hStep;
    let hsl = micropixel.hsl(h, 100, 50);
    let rgb = hsl.toRGB();
    colors.push(rgb);
}

// set each pixel to a different color on the rainbow
for (let i = 0; i < stripLen; i++) {
    let clr = colors[i];
    np.setPixelColor(i, clr)
}

// animate the rainbow
while (true) {
    np.rotate(1);
    np.show();
    basic.pause(30)
}
```

## Supported targets

<a href="https://proto-pic.co.uk/product/micropixel-4x8-ws2812b-board-for-bbc-microbit/" target="_blank" rel="noopener">micro:pixel</a>

<a href="https://proto-pic.co.uk/product/micropixel-edge-for-microbit/" target="_blank" rel="noopener">micro:pixel edge</a>

## License

MIT

## Code of Conduct

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
