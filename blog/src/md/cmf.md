# How the CIE 1931 RGB Color Matching Functions Were Developed from the Initial Color Matching Experiments
*Aug 22, 2020*

## 1. Introduction

When examining the CIE 1931 RGB color matching functions (CMFs), a common, and very reasonable, doubt is: why don't the CMFs take the value of 1 at the wavelengths of the primaries? For instance, at 𝛌 = 700 nm, which is the red primary used in the CIE 1931 RGB space, the R value is 0.0041 (of course the G and B values are 0). What does 0.0041 mean? Shouldn't it be 1 since we need 1 unit of red (𝛌 = 700nm) to match the color of 1 unit of red (𝛌 = 700nm)? Similarly, at 𝛌 = 546.1 nm and 𝛌 = 435.8 nm, which are the green and blue primaries, respectively, used in the CIE 1931 standard, the G and B values are about 0.215 and 0.291, respectively<sup id="a1">[1](#f1)</sup>; they are not 1 either.

No popular graphics textbooks explain this "discrepancy." Numbers on CMFs are mostly just referred to as "weights" or "amounts." For instance, in Real-Time Rendering (4th edition), CMFs are defined as:

> The functions relating each set of matching weights to the test patch wavelengths...

Foundations of Computer Graphics (4th edition) says:

> The amount of each (light) required to match a given wavelength &lambda; is encoded in color matching functions…

These are a bit ambiguous. If the RGB values at 𝛌 = 500 nm are (-0.9494, 1.1727, 0.7767), a reasonable interpretation is that we need -0.9494 units of red, 1.1727 units of green (𝛌 = 546.1 nm), and 0.7767 units of blue (𝛌 = 435.8 nm) to match the color of one unit monochromatic light of wavelength 𝛌 = 500 nm. By that logic, the R value on the red CMF at 𝛌 = 700nm should really be 1.

The [Wikipedia page](https://en.wikipedia.org/wiki/CIE_1931_color_space#CIE_RGB_color_space) on CIE 1931 color space, whoever wrote it, does offer the following remarks:

> Note that rather than specify the brightness of each primary, the curves are normalized to have constant area beneath them.

and:
> The resulting normalized color matching functions are then scaled in the r:g:b ratio of `1 : 4.5907 : 0.0601` for source luminance and `72.0962 : 1.3791 : 1` for source radiance to reproduce the true color matching functions.

These remarks are helpful, as they show that some sort of scaling and normalization happens, so the values on CMF can't be directly interpreted. Nevertheless, three key questions remain:

1. What exactly do values on CMFs mean?
2. Why are the CMFs normalized?
3. How are the CMFs normalized?

These questions could only be answered by going back to the original color matching experiments (now almost 1 century ago) and understanding how the initial experimental data was obtained and subsequently processed to become the 1931 RGB CMFs that we are seeing and using today.

This haunted me for a few days, especially because I wasn't able to find much information about the original experimental data. Then I came across two papers<sup id="a2">[2](#f2)</sup> by Arthur Broadbent [Broadbent 2004a, Broadbent 2004b], who meticulously documented and, in places where the original data is missing, reverse-engineered the truly remarkable process in which the initial color matching experimental data was transformed before being used to construct the CIE 1931 RGB CMFs. Understanding the transformation not only provides historical context, but more importantly allows us to really understand what exactly a CMF is.

## 2. History

There were two color matching experiments conducted in the late 1920's, one by W. D. Wright and the other by J. Guild. Both experiments were conducted independently, but matched incredibly well. Guild combined both sets of data and performed a series of data augmentation (interpolation, smoothing, etc.) to generate the chromaticity data that became the CIE 1931 RGB space, from which the RGB CMFs are constructed.

<p align="center">
    <img src="imgs/history.png">
    <figcaption><b>Figure 1: Steps taken to transform the initial Wright-Guild color matching experimental data to develop the CIE 1931 RGB CMFs.</b></figcaption>
</p>


I show in Figure 1 the process in which the original measurements of Wright and Guild were transformed and eventually used to construct the 1931 CIE RGB CMFs. The RGB space data was then used to develop the XYZ space. The detailed derivation of the RGB to XYZ transformation is nicely summarized in [Fairman 1997]. Another article [Service 2016] summarizes both how the RGB CMFs are derived from color matching experiments and how the RGB space is transformed into the XYZ space. Albeit much less detailed, it offers many insights that are very much worth reading too.

## 3. How to Quantify the Amount of Light in Color Matching Experiments?

The fundamental question is what one unit of light means: one watt, one lumen, or some amount of photons? We could certainly arbitrarily specify one unit of red to be a red light that carries 1 W of power, or one unit of green to be a green light that carries 2 trillion photons, or one unit of blue to be a blue light produces 43 lumen. They are all fine units, but they don't provide us with meaningful information. Instead, units are defined in a way that matches our intuition and satisfies our intuitive requirement. Yes, the notion of units is very subjective. We will go through the major steps in Wright's and Guild's experiments to better understand this.

### 3.1 Wright's Experiment

Wright explained how to determine units in his 1928 paper [Wright 1928]:

> To determine the units of the primaries it is customary to match white by a mixture of the three primaries of the colorimeter in use and to correct the readings as given on the instrument scale by suitable factors, so that for this match all three shall be equal.

The key is that "for this match all three shall be equal", i.e., three equal amounts of primaries should generate white. This is not an objective truth. Far from it, this is just a desirable property that matches our intuition: equal amounts of red, green, and blue produce white.

He then elaborated with an example:

> Suppose that the intensities of the primaries in the colorimeter are controlled by variation of sector openings and that these angular apertures for the white match are 30" for red, 15" for green, and 60" for blue. To correct these readings so that they are in equal proportions, a factor of 2 must be applied to the red and 4 to the green and these factors must be used in any subsequent colour match. Thus, if for a new colour the instrument readings are 40° for red, 10" for green, and 20' for blue, the proportions, in the units agreed upon, are
>
> red 80, green 40, blue 20
>
> giving the trichromatic coefficients as
>
> red 80/140 = .571, green 40/140 = .286, blue 20/140 = .143
>
> from which we have the unit equation for the colour as
>
> `c = .571R + .286G + .143B`

In this example, the actual power of light is controlled by an aperture. Importantly, the relationship between the aperture reading (its angle) and the actual power of the light let out is linear, as the amount of photons let out should be linearly proportional to the area exposed by the aperture, which is linearly proportional to the angle<sup id="a3">[3](#f3)</sup>.

To match white, the aperture is set to be 30° for red, 15° for green, and 60° for blue; we then make an executive call and say that this is the same amount of red, green, and blue. That is, one unit of red is the amount of red light that is let out when the aperture reading is 30°; one unit of green is the amount of green light that is let out when the aperture reading is 15°; one unit of blue is the amount of blue light that is let out when the aperture reading is 60°.

Once we define the units of the primary lights, we can then determine the amounts (i.e., how many units) of primary lights required to match any test light. In Wright's example, for a new color, say C, the aperture readings are 40° for red, 10° for green, and 20° for blue; we then know that we need 40/30 units of red, 10/15 for green, and 20/60 for blue to match C<sup id="a4">[4](#f4)</sup>. These unit amounts translate to a 4:2:1 ratio.

Note that at this point we don't know how much light there is in C. We just know that 40/30 units of red, 10/15 for green, and 20/60 for blue gets us some amount of light C.

To quantify the amount of C, Wright further defined that, when the amount of the constituting red, green, and blue is normalized such that they add up to unity (1), i.e., `0.571 : 0.286 : 0.143`, the resulting light C is said to possess one unit of quantity. The relative red, green, and blue units are called the trichromatic coefficients, which are essentially the same thing as chromaticity coefficients in modern terminology.

```
C = 0.571R + 0.286G + 0.143B.
```

We interpret this equation in two ways. First, one unit of C is defined as mixing 0.571 units of red, 0.286 units of green, and 0.143 units of blue. Second, one unit of C has the same color as mixing 0.571 units of red, 0.286 units of green, and 0.143 units of blue. Note that the amount of light in one unit of red, green, and blue corresponds to real instrument readings, but the amount of light in one unit of C, although is a physical property, is a concept derived from the quantity of red, green, and blue.

In Wright's example, by mixing 40/30 units of red, 10/15 for green, and 20/60 for blue, what we get is 40/30/0.571 = 2.34 units of C. If we change the quantity of C, the amount of red, green, and blue will change proportionally, but the chromaticity coefficients will remain the same. For instance, if we want to get 2 units of C, we should mix 1.142 units of red, 0.572 units of green, and 0.286 units of blue, but we are still mixing the red, green, and blue in a 4:2:1 ratio.

Note that Wright didn't specify the amount of white to be matched when calibrating the units of the primaries. What if we double the power of the white light? The readings of the three primaries will double and become 60°, 30°, and 120° for the red, green, and blue, respectively. Can we define the amount of red light let out at 60° as one unit of red then? Absolutely! Accordingly, one unit of green (blue) would be the amount of green (blue) light let out when the aperture is 30° (120°). The absolute amount of light in each unit of primary light changes, but their ratios remain the same. As a result, the chromaticity coefficients will remain the same.

The notion of "a unit" involves the absolute quantity of light because it is directly related to the aperture reading, which controls the absolute light power, but for the purpose of calculating chromaticity coefficients, the absolute amount of light in a unit doesn't matter - as long as the units are defined such that equal units of primaries generate white. If so, whether a unit of red is 6 watts or 3 watts will not change the chromaticity coefficients for matching any amount of a given light.

In summary, the process of calculating the chromaticity coefficients for any test light is:

1. Mix primaries with a reference white light and define units in a way such that equal units of primaries match the color of some amount of white. The absolute amount of white being matched doesn't matter.
2. Match primaries with a test light and record the instrument readings (e.g., the aperture angle) for the three primaries. Again, the absolute amount of the test light doesn't matter.
3. Convert the instrument reading of each primary to the number of units of that primary using the unit definition in step 1.
4. Normalize the absolute units above such that they add up to 1. The three normalized numbers are the chromaticity coefficients of the test light.

Wright iterated step 2–4 for all the spectral lights from 400 nm to 700 nm at a 10-nm interval, and obtained the chromaticity coefficients of those spectral lights. The primary lights he used were monochromatic lights: 𝛌 = 650 nm for red, 𝛌 = 530 nm for blue, and 𝛌 = 460 nm for green. The reference white he used was the National Physical Laboratory (NPL) standard white (whose exact spectrum power distribution is shown in [Guild 1931], Table I). He had 10 observers for matching the spectral lights and 30 observers for matching the reference white.

<p align="center">
    <img src="imgs/wrights.png">
    <figcaption><b>Figure 2: Chromaticity coefficients of the spectral lights in Wright’s initial experiment. Figure taken from [Wright 1928].</b></figcaption>
</p>

Figure 2, which is taken from [Wright 1928], shows the chromaticity coefficients for all the spectral lights, where each curve represents a particular observer.

### 3.2 A Useful Thought Experiment: Using a Counter-Intuitive Unit System

Here is a useful thought experiment, which will both check your understanding of the idea and be practically useful later in constructing the final CMFs: what if we have a different intuition, where white should be produced by the primaries in a 1:2:3 ratio rather than 1:1:1? Nothing wrong with that. However, the absolute amount in a unit light will change, and thus the chromaticity coefficients will change.

We can reason about it in the following way. Let's say in the old unit system, a certain amount of white light is constructed from one unit of each primary. The same amount of white light will now be constructed by k units of red, 2k units of green, and 3k units of blue in the new unit system. k is a scaling factor that we do not know, as we are just told that the ratio of the primaries to generate white not the exact amount. We thus have this equation:

```
R + G + B = k R' + 2k G' + 3k B',
```

where R, G, and B represent one unit of red, blue, and green light in the old unit system; R', G', and B' represent one unit of red, blue, and green light in the new unit system. Here we have the same amount of the same white light expressed in two different ways. What this means is that :

* One unit red in the old system is equivalent to k units red in the old system
* One unit green in the old system is equivalent to 2k units green in the old system
* One unit blue in the old system is equivalent to 3k units blue in the old system

Now for a test light C that is constructed in the old unit system as:

```
C = 0.571 R + 0.286 G + 0.143 B.
```

It will be constructed by the new unit system as:

```
C = 0.571k R' + 0.286(2k) G' + 0.143(3k) B'.
```

Normalizing the units so that they add up to 1 gets us the definition of one unit of C in the new unit system:


```
C = 0.571k/(0.571k + 0.286(2k) + 0.143(3k)) R' +
    0.286(2k)/(0.571k + 0.286(2k) + 0.143(3k)) G' +
    0.143(3k)/(0.571k + 0.286(2k) + 0.143(3k)) B'
```

Canceling k we have:

```
C = 0.571/(0.571 + 0.286×2 + 0.143×3) R' +
    0.286×2/(0.571 + 0.286×2 + 0.143×3) G' +
    0.143×3/(0.571 + 0.286×2 + 0.143×3) B'.
  = 0.363 R' + 0.364 G' + 0.273 B',
```

which provides us the chromaticity coefficients of C in the new unit system (0.363, 0.364, 0.273).

We have two key observations. First, this new set of coefficients are different from the original set of coefficients (0.571, 0.286, 0.143), simply because we change the way we define units. There are three key factors that dictate the chromaticity coefficients: 1) the choice of primaries; 2) the choice of reference white, and 3) the "intuition" that is used to define units.

Second, the new chromaticity coefficients can be derived from the original chromaticity coefficients without relying on the exact value of k.

A practical problem with Wright's initial data is that it was obtained using Wright's own primaries (Red: 𝛌 = 650 nm, Green: 𝛌 = 530 nm, Blue; 𝛌 = 460 nm). However, at that time the standard primaries used by NPL were different. They are: 𝛌 = 700 nm for red, 𝛌 = 546.1 nm for blue, and 𝛌 = 435.8 nm for green. So in order to compare and match data with others, he needed to somehow transform his data to be based on the NPL standard primaries. Of course he could redo all the experiments using the NPL standard primaries, but that seems a waste of time. As bright as he was, Wright came up with an ingenious way of "synthesizing" results as if the experiments were conducted using the NPL standard primaries based on his original data. This is described in [Wright 1929] and summarized nicely in [Broadbent 2004].

### 3.3 Guild's Experiment

Meanwhile, Guild did a similar experiment using 7 observers, one year before Wright's experiment actually. He used three primary lights of his choice and chose the NPL standard white as the reference white. Importantly, but not surprisingly, the units were defined so that combining equal units of each primary would match the NPL standard white.

The exact spectrums of the primaries were not documented, as Guild simply mentioned that the primaries are obtained by "*passing the light from an opal-bulb gas-filled lamp through red, green and blue gelatine filters.*" This experiment allowed him to get the chromaticity coefficients of all the spectral lights from 380 nm to 700 nm at a 5-nm interval.

<p align="center">
    <img src="imgs/guild.png">
    <figcaption><b>Figure 3: Chromaticity coefficients of the spectral lights in Guild’s initial experiment. Figure taken from [Guild 1930].</b></figcaption>
</p>

Figure 3, which is taken from [Guild 1930], shows the spectral chromatic coefficients from his measurements, where each curve represents an observer. You might have realized that Figure 2 and Figure 3 are slightly different, although they both plot the chromaticity coefficients. The reason is that different primaries, reference white lights, and unit systems were used to derive the results. Figure 2 corresponds to the data from the "Wright 1" step in Figure 1, and Figure 3 corresponds to the data from the "Guild 1" step in Figure 1. Comparing these two figures clearly shows the importance of the primaries, the reference white, and the unit system.

Similar to Wright, he then "synthesized" the data as if the experiment was conducted using the NPL primaries, still targeting the NPL standard white as the reference white.

<p align="center">
    <img src="imgs/comparison.png">
    <figcaption><b>Figure 4: Comparing chromaticity coefficients data from Wright and Guild. The figure is taken from [Broadbent 2004b].</b></figcaption>
</p>

At this point, both Guild and Wright each had data that was in the exact same system (primary color + reference white + unit system), so they could be compared. When Guild combined both sets of data together, they matched well. Figure 4, which is taken from [Broadbent 2004b], compares the two sets of data. Points are from Wright's data, and the lines are from Guild's data. The three colors present the spectral chromaticity coefficients of the three primaries.

The near perfect match is remarkable considering the data was obtained independently using completely different primaries, different observers, and different experimental setups. In addition, the two sets of data that Guild compared weren't even the original experimental data; they are synthesized/transformed from the initial experimental data. The near perfect match gave people confidence in defining a standard for color space.

Guild subsequently did a bit of data augmentation (averaging, curve fitting, smoothing, etc.) to unify his data with Wright's data and presented the final spectral chromaticity coefficients. We usually denote the chromaticity coefficients of a spectral light with a wavelength of 𝛌 as r(𝛌), g(𝛌), b(𝛌).

## 4. Luminance Coefficients: Changing the Reference White

There is one last step before the data could be used to produce the CMFs. CIE 1931 standard doesn't use the NPL standard white as the reference white. Instead, it uses a hypothetical equal-energy white (EEW) that has equal energy/power at all the wavelengths. Guild synthesized the spectral chromaticity coefficients data under the EEW from the data they had, as if they had actually done the experiments using the EEW as the reference white.

Understanding Guild's transformation requires understanding a new, and critical, concept: luminance coefficient, which Guild and Wright called relative luminance or luminosity factor in their papers. Let's see how this transformation works.

The problem setup is the following. We have the chromaticity coefficients of a spectral light 𝛌, based on the NPL standard primaries and using the NPL standard white as the reference white. We need to calculate the new chromaticity coefficients of a spectral light 𝛌, but this time using the EEW as the reference white (still using the NPL standard primaries).

Guild's idea is to figure out how many units of primaries in the old system are needed to match EEW. If we can calculate that, the problem essentially falls back to the thought experiment we had before. How do we get that ratio? We are going to do it wavelength-by-wavelength. Essentially, we are going to figure out for each wavelength 𝛌 in EEW, how many units of primaries in the old system are needed to match its luminance. We will then sum them up to get the total units of primaries, and then normalize them to 1 to get the actual ratio.

Without losing generality, let's assume that the EEW has a power of P at each wavelength in the spectrum. Its monochromatic component at wavelength 𝛌 is expressed using the NPL standard primaries in the old unit system (i.e., where the reference white is NPL standard white) as:

```
W(𝛌) = M(𝛌)(r(𝛌)R + g(𝛌)G + b(𝛌)B),
```

where R, G, B are one unit of red, green, and blue light in the old system, r(𝛌), g(𝛌), b(𝛌) are the chromaticity coefficients of 𝛌 in the old system, and M(𝛌) is an unknown scaling factor such that `M(𝛌)r(𝛌)`, `M(𝛌)g(𝛌)`, and `M(𝛌)b(𝛌)` represent the absolute amount (in terms of the number of units) of red, blue, and green needed to match the EEW at 𝛌. Note that M(𝛌) varies with 𝛌.

Integrating over 𝛌 across the entire spectrum of EEW, we get the total amount (in the number of units) of red, blue, and green needed for EEW:

```
r = ∑M(𝛌)r(𝛌),  
g = ∑M(𝛌)g(𝛌),  
b = ∑M(𝛌)b(𝛌).
```

So the EEW could be expressed as:

```
EEW = rG + gG + bB.
```

How do we get `M(𝛌)`? The key idea here is to explore an invariance: at any wavelength 𝛌, the luminance of the EEW at 𝛌 should match the total luminance from the three constituting primary lights at 𝛌. This is after all how we will generate color for any arbitrary complex spectrum.

Calculating the luminance of the EEW at a particular wavelength 𝛌 is easy: it is `P x V(𝛌)`, where V(𝛌) is the luminance efficiency function (Guild referred to it as photopic efficacy function), which is measured by the CIE at 1924. Here we omit the constant factor (683 lm/W).

But how do we calculate the luminance of the primaries at wavelength 𝛌? We know the absolute amount of red, blue, and green needed at 𝛌 (i.e., `M(𝛌)r(𝛌)`, `M(𝛌)g(𝛌)`, and `M(𝛌)b(𝛌)`), but how do we relate the amount of light to the luminance of the light? The chromaticity coefficients are just ratios and do not say anything about luminance. All they say is that to create the color of any amount of the monochromatic light of a wavelength 𝛌, we need `r(𝛌) : g(𝛌) : b(𝛌)` amount of the primaries. Whatever that arbitrary amount is, the ratio always holds. If we want to match more amount of 𝛌, we need more amount of the primaries, and vice versa. So clearly we need something that encodes the luminance information.

Here is where the notion of luminance coefficients comes into play. Luminance coefficient of a light in a particular system is defined as the luminance of one unit of the light. We usually scale the luminance coefficients of the three primary colors such that the coefficient is 1 for the red primary. In Guild's experiment, he measured and calculated that the luminance coefficients of the three NPL standard primaries, L<sup>r</sup>, L<sup>g</sup>, and L<sup>b</sup>, as the following<sup id="a5">[5](#f5)</sup>:

```
Lʳ = 1.0000,
Lᵍ = 4.4036,
Lᵇ = 0.0471.
```

The total luminance of red, green, and blue at 𝛌 is thus:

```
M(𝛌)r(𝛌)LʳN + M(𝛌)g(𝛌)LᵍN + M(𝛌)b(𝛌)LᵇN,
```

where `N` is an unknown scaling factor.
Thus we have:

```
V(𝛌)P = M(𝛌)r(𝛌)LʳN + M(𝛌)g(𝛌)LᵍN + M(𝛌)b(𝛌)LᵇN.
```

Therefore, `M(𝛌)` is calculated as:

```
M(𝛌) = V(𝛌)P / N(r(𝛌)Lʳ + g(𝛌)Lᵍ + b(𝛌)Lᵇ).
```

Here, `V(𝛌)`, `r(𝛌)`, `g(𝛌)`, `b(𝛌)`, L<sup>r</sup>, L<sup>g</sup>, L<sup>b</sup> are all known. `P/N` is an unknown scaling factor.
Without losing generality, let:

```
U(𝛌) = V(𝛌) / (r(𝛌)Lʳ + g(𝛌)Lᵍ + b(𝛌)Lᵇ),
```

and `P/N = C`. We have:

```
M(𝛌) = U(𝛌)C.
```

Thus,

```
r = ∑M(𝛌)r(𝛌) = C∑U(𝛌)r(𝛌),
g = ∑M(𝛌)g(𝛌) = C∑U(𝛌)g(𝛌),
b = ∑M(𝛌)b(𝛌) = C∑U(𝛌)b(𝛌).
```

That is, the EEW in the old unit system is expressed as:

```
EEW = C∑U(𝛌)r(𝛌) R + C∑U(𝛌)g(𝛌) G + C∑U(𝛌)b(𝛌) B.
```

Therefore, the chromaticity of the EEW in the old unit system is:

<pre>
W<sub>r</sub> = ∑U(𝛌)r(𝛌) / (∑U(𝛌)r(𝛌) + ∑U(𝛌)g(𝛌) + ∑U(𝛌)b(𝛌)),
W<sub>g</sub> = ∑U(𝛌)g(𝛌) / (∑U(𝛌)r(𝛌) + ∑U(𝛌)g(𝛌) + ∑U(𝛌)b(𝛌)),
W<sub>b</sub> = ∑U(𝛌)b(𝛌) / (∑U(𝛌)r(𝛌) + ∑U(𝛌)g(𝛌) + ∑U(𝛌)b(𝛌)).
</pre>

As you can see, the unknown scaling factor `C` (i.e., `P/N`) is irrelevant here. The exact values of W<sub>r</sub>, W<sub>g</sub>, and W<sub>b</sub> are `0.3013`, `0.3140`, and `0.3847` as calculated by Guild<sup id="a6">[6](#f6)</sup>.

This is a significant result, as we now know that the EEW in the old unit system is mixed by red, blue, and green in a W<sub>r</sub>: W<sub>g</sub>: W<sub>b</sub> ratio. We also know that, by definition, the EEW in the new unit system should be mixed with a 1:1:1 ratio. Now the problem essentially falls back to the thought experiment we had before! Using the same method there, we can translate the chromaticity coefficients of any spectral light from the old unit system (i.e., the NPL standard white as the reference white) to the new unit system (i.e., the EEW as the reference white).

One last thing, the luminance coefficients in the new unit system also have to change. The luminance coefficient represents the relative luminance of one unit of light. Since we have changed what one unit means in a new system, the luminance coefficient would certainly have to change. How to calculate that? One unit of red in the new system is equivalent to W<sub>r</sub> units of red in the old system, which has a total luminance of W<sub>r</sub>L<sup>r</sup>, which becomes the luminance of one unit of red in the new system. The same process applies to the green and blue primaries as well. Normalizing the values to red, the exact values of the new luminance coefficients are the following, which will be used to construct the CMFs:

```
Lʳ = 1.0000,
Lᵍ = 4.5907,
Lᵇ = 0.0601.
```

**So let's recap.** What we have calculated so far is the chromaticity coefficients for all the spectral light, assuming the NPL primaries as the primary lights and the EEW as the reference white and a unit system that equal amounts of primary lights produce some amount of reference white, which are the exact same setup as the CIE 1931 standard.

Figure 5 plots the spectral chromaticity coefficients used for the CIE 1931 RGB space. As we can see, the R value is indeed 1 at 𝛌 = 700 nm, the G value is 1 at 𝛌 = 546.1 nm, and the B value is 1 at 𝛌 = 435.8 nm, matching our intuition. But clearly these are not the color matching functions, yet!

<p align="center">
    <img src="imgs/cie1931rgb.png">
    <figcaption><b>Figure 5: Spectral chromaticity coefficients in the CIE 1931 RGB space.</b></figcaption>
</p>

Note the key difference between the luminance coefficient and V(𝛌): V(𝛌) denotes the relative luminance under a unit power and the luminance coefficient denotes the relative luminance under a unit quantity of a light. 1 unit of power is different from 1 unit light - the former quantifies an absolute physical quantity, while the latter is relative to the unit system we use. 1 unit of R could be 100 Watt, and 1 unit of B could be 5 Watt, or could be whatever.

## 5. From Chromaticity Coefficients to CMFs

Now we finally have the data that we can use to produce the CMFs. We will first explain what CMFs are supposed to be used for. Then we will explain how to construct the CMFs from the chromaticity functions so that the CMFs can be used how they are supposed to be used.

### 5.1 Why Do We Need the CMFs?

Remember the goal: generate any color, however complex its Spectral Power Distribution (SPD) is, by mixing the three primary lights. That is, given a SPD, we want to know how many units of the primaries are needed to match its color. This will be done "piece-by-piece": we will calculate the amount of the primaries needed to match the color of the monochromatic light at 𝛌, and then sum the amounts of the primaries together across all the entire spectrum to calculate the total amount of the primaries needed to match the target light. Note that having the chromaticity plot is not enough, since the SPD has absolute power numbers, and the chromaticity tells us just the ratio.

Without losing generality, let's say we have a target light T whose SPD is 𝚽; with a power 𝚽(𝛌) at the wavelength 𝛌. How do we know how many units of the primaries are needed to match the monochromatic light at 𝛌? Chromaticity coefficients r(𝛌), g(𝛌), and b(𝛌) tell us only in what ratio we should mix the primaries in order to get one unit of the monochromatic light 𝛌, but we want to find out how much primaries to mix to get 𝚽(𝛌) Watt of the monochromatic light 𝛌.

This is where the CMFs come into play. Color matching functions, R(), G() and B(), are defined as the amount of red, green, and blue (in terms of units) that are needed to match the luminance of 1 unit power of each monochromatic light 𝛌 over 𝚽. Knowing the CMFs thus allows us to calculate the amount of the primaries needed at 𝛌 for the target light T. Here is how.

The power of the target light T at 𝛌 is 𝚽(𝛌). Since matching the luminance of one watt of monochromatic light at 𝛌 requires R(𝛌) units of red, G(𝛌) units of green, and B(𝛌) units of blue, matching the luminance of monochromatic light 𝛌 in the target light T naturally requires 𝚽(𝛌)R(𝛌) units of red, 𝚽(𝛌)G(𝛌) units of green, and 𝚽(𝛌)B(𝛌) units of blue.

As a result, to match the entire target light, we need `∑𝚽(𝛌)R(𝛌)` units of red, `∑𝚽(𝛌)G(𝛌)` units of green, and `∑𝚽(𝛌)B(𝛌)` units of blue. If you have a continuous SPD rather than discrete samples, we get the following familiar equations:

<!-- https://stackoverflow.com/questions/32550310/html-display-new-line-in-code-tag -->
<pre>
R<sub>t</sub> = ∫𝚽(𝛌)R(𝛌) d𝛌,
G<sub>t</sub> = ∫𝚽(𝛌)G(𝛌) d𝛌,
B<sub>t</sub> = ∫𝚽(𝛌)B(𝛌) d𝛌.
</pre>

R<sub>t</sub>, G<sub>t</sub>, and B<sub>t</sub> are the absolute amount of red, green, and blue needed to match the color of target light T with a SPD 𝚽. The ratio of R<sub>t</sub>, G<sub>t</sub>, and B<sub>t</sub> are the chromaticity coefficients of the target light T, i.e., the amount of red, blue, and green needed to produce one unit of T. In modern terminology, R<sub>t</sub>, G<sub>t</sub>, and B<sub>t</sub> are the tristimulus values of T.

### 5.2 How to Construct the CMFs?

Now we can use the CMFs to generate colors, the next question is how do we construct the CMFs?

Remember that the chromaticity coefficients r(𝛌), g(𝛌), and b(𝛌) tell us in what ratio we should mix the primaries in order to get one unit of the monochromatic light 𝛌. Now assume that to match the luminance of 1 unit power of a monochromatic light 𝛌 we need `k(𝛌)r(𝛌)`, `k(𝛌)g(𝛌)`, and `k(𝛌)b(𝛌)` units of the primaries. Note that we need to maintain the `r(𝛌) : g(𝛌) : b(𝛌)` ratio to retain the chromaticity of the monochromatic light 𝛌.

The luminance of one unit power of the monochromatic light 𝛌 is V(𝛌). The total luminance given by the the primaries at 𝛌 is <code>L<sup>r</sup>k(𝛌)r(𝛌) + L<sup>g</sup>k(𝛌)g(𝛌) + L<sup>b</sup>k(𝛌)b(𝛌)</code>. Therefore, <code> V(𝛌) = L<sup>r</sup>k(𝛌)r(𝛌) + L<sup>g</sup>k(𝛌)g(𝛌) + L<sup>b</sup>k(𝛌)b(𝛌), </code>

which allows us to calculate k(𝛌):

```
k(𝛌) = V(𝛌) / (Lʳr(𝛌) + Lᵍg(𝛌) + Lᵇb(𝛌)).
```

The denominator, L<sup>r</sup>r(𝛌) + L<sup>g</sup>g(𝛌) + L<sup>b</sup>b(𝛌), could be seen as the luminance coefficient of the monochromatic light of a wavelength 𝛌, which is an instance of applying the linear system assumption to the luminance coefficient.

With k(𝛌), we get the three CMFs:

```
R(𝛌) = k(𝛌)r(𝛌) = V(𝛌)r(𝛌) / (Lʳr(𝛌) + Lᵍg(𝛌) + Lᵇb(𝛌)),
G(𝛌) = k(𝛌)g(𝛌) = V(𝛌)g(𝛌) / (Lʳr(𝛌) + Lᵍg(𝛌) + Lᵇb(𝛌)),
B(𝛌) = k(𝛌)b(𝛌) = V(𝛌)b(𝛌) / (Lʳr(𝛌) + Lᵍg(𝛌) + Lᵇb(𝛌)).
```

The CMFs can be seen as scaled from the original r(𝛌), g(𝛌), and b(𝛌). Note that k(𝛌) is a function of 𝛌, not a constant. These are the CMFs for CIE 1931 RGB.

<p align="center">
    <img src="imgs/cmfcompare.png">
    <figcaption><b>Figure 6: CIE CMFs (solid) and CMFs using Wright-Guild data, where the reference white is the NPL standard white rather than the EEW.</b></figcaption>
</p>

An important attribute of the CMFs is that they depend on the primary colors as well as the reference white light. The fact that it depends on the reference white is often ignored but an important one! This is because the reference white will decide what "one unit" means for the primary colors, and thus affect r(𝛌), g(𝛌), b(𝛌), L<sup>r</sup>, L<sup>g</sup>, L<sup>b</sup>.

Figure 6 shows the CIE 1931 RGB CMFs (solid curves), and as a comparison, we also overlay the CMFs if the combined Wright-Guild data was used (dotted curves), i.e., using the NPL standard white as the reference white rather than the EEW. As we can see, while the trends are similar, the CMFs in the two systems are different. This illustrates that the CMFs are indeed dependent on the choice of the reference white.

### 5.3 Properties of the CIE 1931 RGB CMFs

1, The EEW has the same RGB tristimulus values, because the unit system is defined such that the EEW is mixed with an equal amount of the primaries. Thus,

```
∫𝚽(𝛌)R(𝛌)d𝛌 = ∫𝚽(𝛌)G(𝛌)d𝛌 = ∫𝚽(𝛌)B(𝛌)d𝛌.
```

Since in the EEW 𝚽(𝛌) is 1 (constant) everywhere, we have:

```
∫R(𝛌)d𝛌 = ∫G(𝛌)d𝛌 = ∫B(𝛌)d𝛌
```

These integrations represent the area under the CMF curves. This is why the Wikipedia page cited at the very beginning says "*Note that rather than specify the brightness of each primary, the curves are normalized to have constant area beneath them.*"

2, By definition, for any 𝛌, we have <code>V(𝛌) = L<sup>r</sup>R(𝛌) + L<sup>g</sup>G(𝛌) + L<sup>b</sup>B(𝛌)</code>. Both sides of the equation represent the luminance of 1 W monochromatic light at 𝛌.

Integrate both sides:

```
∫V(𝛌)d𝛌 = ∫LʳR(𝛌)d𝛌 + ∫LᵍG(𝛌)d𝛌 + ∫LᵇB(𝛌)d𝛌  
        = Lʳ∫R(𝛌)d𝛌 + Lᵍ∫G(𝛌)d𝛌 + Lᵇ∫B(𝛌)d𝛌
```

Since `∫R(𝛌)d𝛌 = ∫G(𝛌)d𝛌 = ∫B(𝛌)d𝛌`, we have:

```
∫R(𝛌)d𝛌 = ∫G(𝛌)d𝛌 = ∫B(𝛌)d𝛌 = ∫V(𝛌)d𝛌/(Lʳ + Lᵍ+ Lᵇ),
```

Not only the areas under the three CMF curves are the same, their absolute area is the area under V(𝛌) scaled by <code>1/(L<sup>r</sup> + L<sup>g</sup>+ L<sup>b</sup>)</code>.

3, Today the spectral chromaticity coefficients of the CIE 1931 RGB space are calculated based on the CMFs R(𝛌), G(𝛌), and B(𝛌). But as we have seen, Guild and Wright had first calculated the spectral chromaticity coefficients r(𝛌), g(𝛌), and b(𝛌), from which the CMFs are derived. Does calculating the spectral chromaticity coefficients from the CMFs change the actual spectral chromaticity coefficients?

No. As we can see from this Equation, `R(𝛌) : G(𝛌): B(𝛌)` is the same as `r(𝛌) : g(𝛌) : b(𝛌)`. The former merely scales the latter by k(𝛌), which is the same for a given 𝛌.

4, The L<sup>r</sup>, L<sup>g</sup>, and L<sup>b</sup> in the CIE 1931 standard are 1.000, 4.5907, and 0.0601 as calculated in this Equation. That's why the Wikipedia page says "*The resulting normalized color matching functions are then scaled in the r:g:b ratio of `1 : 4.5907 : 0.0601` for source luminance...*"

What it really means is that <code>𝚽(𝛌)L<sup>r</sup>R(𝛌)</code>, <code>𝚽(𝛌)L<sup>g</sup>G(𝛌)</code>, and <code>𝚽(𝛌)L<sup>b</sup>B(𝛌)</code> represent the luminance of red, green, and blue needed to match the luminance of 𝚽(𝛌).

<p align="center">
    <img src="imgs/luminance.png">
    <figcaption><b>Figure 7: The luminance of EEW and the luminance of the three primaries.</b></figcaption>
</p>

Figure 7 plots the luminance of the EEW and the three primaries. As we can see, blue contributes very little to the luminance, which shouldn't be too surprising in that the amount of S cones is only about 2% - 7% on the retina [Roorda 1999].

5, We know that L<sup>r</sup>R(𝛌), L<sup>g</sup>G(𝛌), and L<sup>b</sup>B(𝛌) represent the luminance of red, green, and blue in matching the luminance of 1 W of the monochromatic light 𝛌. How about the power of each primary in matching the luminance of 1 W of the monochromatic light 𝛌?

The power of red light is <code>L<sup>r</sup>R(𝛌)/V(700)</code>, which is scaled from R(𝛌) by a factor of <code>L<sup>r</sup>/V(700)</code>. Similarly, to calculate the power of green and blue, we scale G(𝛌) and B(𝛌) by <code>L<sup>g</sup>/V(546.1)</code> and <code>L<sup>b</sup>/V(435.8)</code>. The relative ratio <code>L<sup>r</sup>/V(700) : L<sup>g</sup>/V(546.1) : L<sup>b</sup>/V(435.8)</code> is `72.0962 : 1.3791 : 1` when normalized.

This is why the Wikipedia page says "*The resulting normalized color matching functions are then scaled in the r:g:b ratio of ... 72.0962 : 1.3791 : 1 for source radiance.*" What this really means is that when red, green, and blue light are mixed to match the luminance of 1 W of the monochromatic light 𝛌, their relative power ratio `72.0962R(𝛌) : 1.3791G(𝛌) : B(𝛌)`<sup id="a7">[7](#f7)</sup>.

6, Here is one accurate way to calculate the luminance coefficients L<sup>r</sup>, L<sup>g</sup>, and L<sup>b</sup> even if they are not given. Since the reference white should have the same tristimulus values:

```
∫𝚽(𝛌)R(𝛌)d𝛌 = ∫𝚽(𝛌)G(𝛌)d𝛌 = ∫𝚽(𝛌)B(𝛌)d𝛌.
```

If we sample the SPD at enough points, the integration becomes a summation:

```
∑𝚽(𝛌)k(𝛌)r(𝛌) = ∑𝚽(𝛌)k(𝛌)g(𝛌) = ∑𝚽(𝛌)k(𝛌)g(𝛌),
```

Plugging in <code>k(𝛌) = V(𝛌) / (L<sup>r</sup>r(𝛌) + L<sup>g</sup>g(𝛌) + L<sup>b</sup>b(𝛌))</code>:

```
∑V(𝛌)𝚽(𝛌)r(𝛌) / (Lʳr(𝛌) + Lᵍg(𝛌) + Lᵇb(𝛌)) =  
∑V(𝛌)𝚽(𝛌)g(𝛌) / (Lʳr(𝛌) + Lᵍg(𝛌) + Lᵇb(𝛌)) =  
∑V(𝛌)𝚽(𝛌)b(𝛌) / (Lʳr(𝛌) + Lᵍg(𝛌) + Lᵇb(𝛌)),
```

where V(𝛌), 𝚽(𝛌), r(𝛌), g(𝛌), and b(𝛌) are all known. So we have three variables L<sup>r</sup>, L<sup>g</sup>, and L<sup>b</sup>, and two equations, which should allow us to get the ratios of the variables.

From how CIE XYZ data was generated from CIE RGB [Fairman 1997], it looks like this fitting method was actually what was done to get the L<sup>r</sup>, L<sup>g</sup>, and L<sup>b</sup>, rather than using the transformed data from the original Wright-Guild experiments.

## 6. Acknowledgement

I wanted to express my gratitude to Arthur Broadbent so much. His reverse engineering and meticulous documentation of the process is almost magic. The email address he used in his papers, abroadbe@ubishops.ca, isn't reachable. If someone could tell me how to contact him so that I can thank him that'll be great!

## References

[Wright 1928] A re-determination of the trichromatic coefficients of the spectral colours. Trans Opt Soc London 1928–29;30:141–164

[Wright 1929] A re-determination of the mixture curves of the spectrum. Trans Opt Soc London 1929–30;31:201–211.

[Guild 1931] The colorimetric properties of the spectrum. Philos Trans Roy Soc London Ser. A 1931;230:149–187.

[Broadbent 2004a] A Critical Review of the Development of the CIE1931 RGB Color-Matching Functions. Color Research & Application 29(4):267–272. August 2004.

[Broadbent 2004b] Calculation from the original experimental data of the CIE 1931 RGB standard observer spectral chromaticity co-ordinates and color matching functions.

[Fairman 1997] Fairman HS, Brill MH, Hemmendinger H. How the CIE1931 color- matching functions were derived from the Wright–Guild data. Color Res Appl 1997;22:11–23.

[Service 2016] The Wright - Guild Experiments and the Development of the CIE 1931 RGB and XYZ Color Spaces. https://philservice.typepad.com/Wright-Guild_and_CIE_RGB_and_XYZ.pages.pdf.

[Roorda 1999] The arrangement of the three cone classes in the living human eye. Nature volume 397, pages 520–522(1999).

## Notes

<!---
https://stackoverflow.com/questions/25579868/how-to-add-footnotes-to-github-flavoured-markdown
--->
<b id="f1">1</b> These are not exact numbers, since CIE 1931 RGB CMFs are published at a 5-nm interval. I used the closest values: 545 nm for G and 435 nm for B. To obtain more accurate values, one could fit the CMFs using high-order polynomials, as is the standard practice in Colorimetry. [&leftarrow;](#a1)

<b id="f2">2</b> The second paper is probably not officially published and is largely an expanded version of the first paper, but the second paper is written in a more approachable narrative. [&leftarrow;](#a2)

<b id="f3">3</b> In practice, this relationship depends on the arrangement of knobs used in the experiments, and could be linear, logarithmic, or others. Theoretically, it. doesn’t matter how the knob reading and the power/luminance/amount of photons of the light let out, as long as we can experimentally record the mapping from one to the other. But since the aperture reading is a proxy of the amount of light, the linear relationship is needed to apply Grassmann’s linear law of color. [&leftarrow;](#a3)

<b id="f4">4</b> This assumes that the aperture reading and the light power are linearly correlated as mentioned earlier, which allow us to apply Grassmann’s linear law of color. [&leftarrow;](#a4)

<b id="f5">5</b> Actually, the numbers reported in Guild’s 1931 paper is: <code> L<sup>r</sup> = 1.0000, L<sup>g</sup> = 4.390, L<sup>b</sup> = 0.048. </code> Broadbent realized that Guild made a mistake, which Guild later corrected, but Guild never published the correct results. Broadbent \[Broadbent 2004a, Broadbent 2004b\] reverse engineered the data backwards from the CIE 1931 RGB data, which is what we are using here. [&leftarrow;](#a5)

<b id="f6">6</b> Again, this is the corrected result by Broadbent. [&leftarrow;](#a6)

<b id="f6">7</b> The power ratio and radiance ratio is the same, given the same area and solid angle. [&leftarrow;](#a7)
