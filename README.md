# BetterVectorDrawable
The VectorDrawable implementation for Android 4.0+

*Keywords: vector drawable, support library, compat, svg*

## Attaching the library
The lib is deployed to the Maven Central Repository, so just add this line to the **build.gradle** dependencies section:
```
compile '::0.1+'
```

## Usage
### Inflating
```
Drawable drawable = VectorDrawableCompat.inflate(getResources(), R.drawable.your_vector);
```
There is an overload of this method with the first boolean parameter, which allows to force system handling of vector drawables on Android 5.0+.
However, using it is not recommended, because system implementation of VectorDrawable does not support some extra features, like path’s fillType attribute (see below).

### Referencing vector drawables from other XML resources
It is possible to reference vector drawables from other drawable or layout XML resources as regular drawables.
For this feature to work, you need to enable resource interception.

Extend the `Application` class and override the `onCreate()` method. Do not forget to modify **AndroidManifest.xml** accordingly (see the demo app).

Call
```
VectorDrawableCompat.enableResourceInterceptionFor(getResources(), …);
```
from the onCreate method. The last parameter is a list of vector resource IDs for which interception must be enabled.

There are three ways to obtain the list. If you are not sure what way to choose, try to measure the performance of the each method.

#### Very convenient, but relatively slow
```
int[] ids = VectorDrawableCompat.findAllVectorResourceIdsSlow(getResources(), R.drawable.class);
VectorDrawableCompat.enableResourceInterceptionFor(getResources(), ids);
```
This method scans through all drawable XML resources and ensures that each returned resource has vector data inside.
Usually it is OK to use this method by default. Nevertheless, it may increase application startup time on old devises if the app has many drawable resources.

#### Less convenient, but faster
```
int[] ids = VectorDrawableCompat.findVectorResourceIdsByConvention(getResources(), R.drawable.class, Convention.ResourceNameHasVectorSuffix);
VectorDrawableCompat.enableResourceInterceptionFor(getResources(), ids);
```
This method uses resource naming convention to find vector resources.
It relies on that every vector resource name matches `vector_*` pattern or `*_vector` pattern, or both. This can be controlled by the `resourceNamingConvention` parameter.

#### The fastest, but inconvenient
```
VectorDrawableCompat.enableResourceInterceptionFor(getResources(),
    R.drawable.your1_vector,
    R.drawable.your2_vector,
    R.drawable.your3_vector);
```
Just enumerate vector drawable resource IDs manually.

## Converting SVGs to vector drawables
> When parsing XML resources, Android ignores XML attributes that aren’t supported by the current device.

This is why you have to duplicate all `android:` attributes in the vector drawable XMLs. For example:
```
```

To make it easier for you, use **SVG to VectorDrawable Converter** https://github.com/a-student/SvgToVectorDrawableConverter

See section **BetterVectorDrawable library** there for the converter command, which produces vector drawables understandable by the lib.

## Path’s fillType attribute
The lib additionally supports specifying `better:fillType` attribute on a path. This gives you an ability to control how the inside of the path is determined.
It corresponds to the **fill-rule** attribute in SVG and accepts two values: `winding` (**nonzero** in SVG) and `even_odd` (**evenodd** in SVG).

## Issues
If you have any problems with the lib, please create an issue on GitHub (https://github.com/a-student/BetterVectorDrawable/issues/new)
and explain the reproducing steps.
