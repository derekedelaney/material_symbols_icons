# Material Symbols Icons for Flutter

## (with full variation support and automatic code generation capability for updating icon definition files)

Why another version of this for flutter ? Because every other package for material icons is either out of date or has *INCORRECT* and *MISSING* icon definitions.
These icons also support the complete set of icon variation parameters defined for the Material Symbols Icons.  This includes setting fill/not filled, various weights, grades and optical sizes.
This package contains a code generation dart file which automatically downloads the latest versions of the Material Symbols Icon font files from [https://github.com/google/material-design-icons](https://github.com/google/material-design-icons) and generates updated dart files containing all of the latest defined icons.  No more missing icons!

## [Live Example](https://timmaffett.github.io/material_symbols_icons)

Here is a [live example](https://timmaffett.github.io/material_symbols_icons) of the current version of this package where you can test any Material Symbols icon name to verify it's availability.  The example also allows playing with all of the font variation options to explore further customizing the look of our Material Symbols.

This package includes an automatic generator program so that the user has the option of RE-generating the package at any time with the most current Material Symbols Icons definitions.  This program downloads the latest Material Symbols fonts from the github repository [https://github.com/google/material-design-icons](https://github.com/google/material-design-icons), and their corresponding codepoint definition files.  It will then automatically create the material_symbols_icons.dart definition source file.    This automatic generations allows this package to use Github CI routines to ensure that it is always up to date and in-sync with the latet Material Symbols defintions.

(For users of the previous Flutter alternatives for Material Symbols Icons support - No more finding an icon on [https://fonts.google.com/icons?icon.style=Outlined](https://fonts.google.com/icons?icon.style=Outlined) and then trying to use it's name, and then discovering that it is missing, or worse yet having the incorrect icon appear!)


There are several options with how you reference the icons within your flutter program:

1) If you are using icons from a single style (outlined, rounded or sharp) then you can import:
  A) `package material_symbols_icons\outlined.dart` and using `MaterialSymbols.iconname` to reference outlined style icons.
  B) `package material_symbols_icons\outlined_suffix.dart` and using `MaterialSymbolsOutlined.iconname` to reference outlined style icons
  C) `package material_symbols_icons\rounded.dart` and using `MaterialSymbols.iconname` to reference rounded style icons.
  D) `package material_symbols_icons\rounded_suffix.dart` and using `MaterialSymbolsRounded.iconname` to reference rounded style icons.
  E) `package material_symbols_icons\sharp.dart` and using `MaterialSymbols.iconname` to reference sharp style icons.
  F) `package material_symbols_icons\sharp_suffix.dart` and using `MaterialSymbolsSharp.iconname` to reference sharp style icons.

  Importing the A), C) or E) (`outlined.dart`, `rounded.dart` or `sharp.dart`) versions allows you to easily swap any style out for an alternate, with changing the `import` statement being the only change in your code that is needed to switch to the alternate.  (This is becsause these files all use `MaterialSymbols` as the class and the identical icon names (with no suffix) for each icon).

  Options B), D) and F) (`outlined_suffix.dart`, `rounded_suffix.dart` or `sharp_suffix.dart`) have the style name as a suffix to the `MaterialSymbols` class name (`MaterialSymbolsOutlinded`, `MaterialSymbolsRounded` and `MaterialSymbolsSharp` respectively), thus allowing more than one of them to be used simultaneously without name collisions. (ie. you could use `MaterialSymbolsRounded.developer_mode` in one place and `MaterialSymbolsSharp.check_box` in another if you prefered the rounded look for the `developer_mode` icon and the sharp look for the `check_box` icon).

2) Alternatitely if you use icons from more than one of the styles, (or are coming from one of the previous Material Symbols Icon package) then importing `package material_symbols_icons\universal.dart` and using `MaterialSymbols.` as the base class.  This class contains outlined, rounded and sharp versions of every icon.  You access them using `MaterialSymbols.iconname_outlined`, `MaterialSymbols.iconname_rounded` or `MaterialSymbols.iconname_sharp`
This is the largest of the options as it includes all 3 versions of the Material Symbols variable fonts (outlined, rounded and sharp).  I would imagine that most users will pick the style of the icons they prefer and use one of the specific classes from #1.  This option is included primarily for users coming from one of the pre-existing packages.


--------------------------------------;

ALl icon names that start with a number (like `360` or `9M`) but have their icon names prefixed with a `$` to make the names valid dart class member names.
SO if you want to access the icon with the name `360` you use `MaterialSymbols.$360` instead.

Additionally the iconnames `class`, `switch` and `try` have been renamed with a leading underscore also (`$class`, `$switch` and `$try`) as these are dart language reserved words.

## Note on icon tree shaking

Flutter has a wonderful capability of 'tree shaking' icons when making release builds.  This can often reduce the file size of the icons fonts by >99%.  It does this removing all icons from the fonts which are not used by your application.
HOWEVER, this method does have it's limitations.  Due to how the tree shaking works if a font is _never_ referenced by your application then the font is _still included in its entirety_ - because the font is never used at all no tree shaking is triggered for the font.   This creates a problem for our class because the `outlined`, `rounded` and `sharp` style icons are each provided by a separate font.  If you are only using outlined icons and never rounded or sharp icons the outlined font will be tree shaken but the rounded and sharp's _ENTIRE_ fonts will be included.  This can be >10MB of extra, completely unused, space consumed by your app.   This package contains a simply solution for this problem - one additional call to `MaterialSymbolsBase.forceCompileTimeTreeShaking()` needs to be added somewhere within your app, typically in the `main()`.
This call will force a reference to each of the 3 possible styles of material symbols icons, and the unused fonts will be reduced in size by >99.99%.

I understand requiring a call to `MaterialSymbolsBase.forceCompileTimeTreeShaking()` is not ideal - and an issue has been submitted with the flutter team to suggest any better alternatives (like fixed tree shaking to also be triggered by the new flutter 3 `@staticIconProvider` annotation (which was added to allow for tree shaking for web based flutter apps)

```dart
void main() {
  /*
    This called forces a reference to each of the 3 possible Material Symbols Icon fonts
    so that tree shaking can take place and unused fonts will be removed.  If 
    this is NOT done then any un-referenced fonts (such as rounded and sharp if you were
    using outlined) will NOT BE SHOOK from the tree and your executable will include
    these!
    (Strictly speaking in this example, where we reference every icon in every font style, 
    this is not needed, but in real world this is ALWAYS needed, so it is included here.
  */
  MaterialSymbolsBase.forceCompileTimeTreeShaking();

   ...
}
```

--------------------------------------;

The variable version so the Material Symbols fonts are used, so it is possible to further modify (or animate!) the icons by specifying your own parameters for fill, weight, grade, and optical size when creating your icons.

```dart
    
    final myIcon = Icon( MaterialSymbols.settings, fill: 1, weight: 700, grade: 0.25, opticalSize: 48 );

```

]You can also set application wide defaults using your `IconThemeData` within your Theme.

```dart
class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    /*
      Set default IconThemeData() for ALL icons
    */
    return MaterialApp(
      title: 'Material Symbols Icons For Flutter',
      theme: ThemeData(
        primarySwatch: Colors.teal,
        useMaterial3: true,
        fontFamily: 'Roboto',
        iconTheme: const IconThemeData(color: Colors.black, fill: 0, weight: 100, opticalSize: 48),
      ),
      home: const MyHomePage(title: 'Material Symbols Icons For Flutter'),
    );
  }
}
```

If you find yourself using more than one of the styles simultaneously this package has a method of specifying default variations on a *per* style basis using:

```dart
    MaterialSymbolsBase.setOutlinedVariationDefaults(
        color: Colors.red,
        fill: 1,
        weight: 300,
        grade: 0,
        opticalSize: 40.0);
    MaterialSymbolsBase.setRoundedVariationDefaults(
        color: Colors.blue,
        fill: 0,
        weight: 400,
        grade: 200,
        opticalSize: 48.0);
    MaterialSymbolsBase.setSharpVariationDefaults(
        color: Colors.teal,
        fill: 0,
        weight: 600,
        grade: 0.25,
        opticalSize: 20.0);
```

Is the `setXYZVariationDefaults()` methods are used then the icons need to be created using `VariedIconExt.varied()` call instead of `Icon()` directly.

-------------------------------------;
[From https://github.com/google/material-design-icons/raw/master/README.md](https://github.com/google/material-design-icons/raw/master/README.md)
## Material Symbols

These newer icons can be browsed in a more user-friendly way at https://fonts.google.com/icons. Use the popdown menu near top left to choose between the two sets; Material Symbols is the default.

These icons were built/designed as variable fonts first (based on the 24 px designs from Material Icons). There are three separate Material Symbols variable fonts, which also have static icons available (but those do not have all the variations available, as that would be hundreds of styles):

- Outlined
- Rounded
- Sharp
- Note that although there is no separate Filled font, the Fill axis allows access to filled styles—in all three fonts.

Each of the fonts has these design axes, which can be varied in CSS, or in many more modern design apps:

- Optical Size (opsz) from 20 to 48 px. The default is 24.
- Weight from 100 (Thin) to 700 (Bold). Regular is 400.
- Grade from -50 to 200. The default is 0 (zero).
- Fill from 0 to 100. The default is 0 (zero).

What is currently _not_ available in Material Symbols?
- only the 20 and 24 px versions are designed with perfect pixel-grid alignment
- the only pre-made fonts are the variable fonts
- there are no two-tone icons
