## The purpose of the code changes
The main goal was to not have to hardcode the colors, and to be able to generate them despite the indexing system.

## How it works?
The color generation is made by leveraging the @mixins feature of sass.

The original `.nav-folder-title` rule, is now parameterized inside a function, that accepts a **dataPath** and a **color**.
```scss
// Passing dataPath and color as parameters
@mixin nav-folder-title($dataPath, $color) {

	.nav-folder-title[data-path^='#{$dataPath}'] {
        // allows the reuse of the color here, ...
		color: $color;
        // ... here, ...
		--nav-item-color-hover: color-mix(
			in srgb,
			#{$color} var(--fg-contrast-amount),
			var(--contrast-color)
		);
        // ... here again, ...
		--nav-item-background-hover: color-mix(
			in srgb,
			#{$color} var(--bg-contrast-amount),
			transparent
		);
        // ... you get the point.
		--background-modifier-border-focus: color-mix(
			in srgb,
			#{$color} 40%,
			transparent
		);
		--nav-collapse-icon-color: color-mix(
			in srgb,
			#{$color} 60%,
			transparent
		);
	}
    // ... Subfolders rules also get the variables
}
```

After the definition of the @mixin, the rules themselves can be generated with loops.
An example for the folder structure used in the videos:
```scss
// Loop every folder name from 0 to 99 
@for $i from 0 through 99 {
  //generate a random color for each iteration
  $color: generateColor($i, 100);
  $dataPath: $i;
  @if $i < 10 {
    $dataPath: "0#{$i}"    
  }
  //finally, call the @mixin with the variables for each iteration
  @include nav-folder-title("#{$dataPath}", $color)
}
```

This generates the following:
![standard colors](/readme_images/standard_colors.gif)

## Customization
A few variables exist that allow the customization of the generated colors.
For example by manipulating **$range** and **$offset**, one can limit how much of the color wheel is used, and where it starts.
The following example shows a **$range** of 20% of the color wheel, starting at the light blues range (**$offset** at 170)
![blue colors](/readme_images/blues.gif)

There are also the **$saturation** and **$lightness** variables to control the general itensity of the colors.
The final example is my personal settings for the Amoled Vanilla theme, with my folder indexing scheme.
```scss
$total_combinations: 36;
$range: 90; //almost all the wheel. this avoids the first and last colors having similar colors
$offset: 280; //starts at purples
$saturation: 100%;
$lightness: 55%;
```
![xvt](/readme_images/xvt_example.gif)