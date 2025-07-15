
There are two viable approaches, which can be combined

# Tailwind Colors
Tailwind is awesome, and so is the color management. Follow the guide to configure your pallet.

# HSL (OG way)
``` css
:root {
	--base-color: hsl(223, 18%, 20%);
	--primary-color: hsl(from var(--base-color) h s calc(l + 10));
	--secondary-color: hsl(from var(--base-color) h s calc(l + 20));
	--base-darker: hsl(from var(--base-color) h s calc(l - 5));
	--base-black: hsl(from var(--base-color) h s calc(l - 10));
}
```
