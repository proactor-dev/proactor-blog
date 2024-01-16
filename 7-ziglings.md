# Ziglings

[Ziglings](ttps://ziglings.org) is a collection of failing programs, which shall be fixed as exercises. Goal is to learn Zig language basic features and functionalities.

Ziglings is inspired by Rust languages famous [Rustlings](https://github.com/rust-lang/rustlings), which shares the similar idea - although the exercises are totally different for both languages. At the time of writing both have around 100 exercises and are (at least mostly) fun to work with.

## Set up

Ziglings exercices and installation instructions can be found https://ziglings.org

>Note: Development version if Zig compiler is needed, so default versions from package managers may not be sufficient to run Ziglings.

## Background

I started playing with Ziglings 30th of Dec 2023. Main goal was to understand the difference between Rust, Nim and Zig, which has been the trio of the languages I have been watching closely during last couple of years.

The main motivation for me on choosing my "next big language" has been to find something to replace Python as a multipurpose tool and to find something more heavy duty low level, static typing and without runtime.

Mostly I was interested on the "look and feel" of Zig compared to the other two, since I have some issues with both Rust and Nim, which I hope Zig would overcome.

It's a long topic discussing personal preferences between Rust and Nim, but to cut it short, these are the main points a keep wondering about.

### Rust
1. I very much like Rust overall feeling and how complete the developer experience is with Cargo and testing inbuild.
2. I like Rust's implementation of returning errors as values, which seems and feels very elegant.
3. I like Rust's nice modern functional programmish concepts like match function, which makes the language feel up to date and powerfull.
4. On the other hand, however tempting it would be to master Rust, the language feels very verbose and overly complicated to my limited capasity and time to learn new things.

### Nim
1. I like Nim's simplicity and not forcing developers use any specific paradigm.
2. I like Nim's recent switch from carbage collected memory management to non GC memory management. I don't know if memory management would really matter in my use cases, but I just want things be as low level as possible.
2. I dislike Nim's Python like "look and feel" with intendations and CamelCases. It feels kind of stupid to judge language based on such secondary issues, but on the other hand I just cannot help myself feeling such way.

Based on the above I'm constantly leaning towards learning Rust, but on the other hand I don't know if I'm (yet?) ready for that. Thus my interest on seeking options elsewhere and thus Zig.

## Zig

What interest me in Zig is, that it provides the similar mechanism as Rust for returning errors as values and has a promise of being more straighforward language to learn and be sooner productive with.

On the other hand I know Zig is still in it's very early development phase missing all the fancy "Cargoish" tools as Rust has. Thus it will be interesting to see how I will find it throuhg experimenging Ziglings excerices.

Let's finally get on board and give it a try!


## Ziglings

Ziglings is run with `zig build` command, which runs the series of exercices one by one. "Running" in this context means that the zig compiler tries to compile and reports the error that the user needs to fix.

At the time of writing Ziglings (Version-0.12.0-dev.1243) is the latest available and it is running against Zig Version 0.12.0-dev.1859. Both Ziglings and Zig language itself are still both rapidly changing. For example 'async' exercises are not runnable at the moment, still language support is currently lacking.

I started my journey with Ziglings, so that I was solving couple to few exercises per each evening. I was also (re)doing Rustlings at the same time and was jumping between the languages during first days. I had started Rustlings earlier, but had gone only midway and now wanted to get those also finally "out-of-the-way".

Soon however my interest was drawn to Ziglings and I started focusing on them and left Rustlings wait for later. Main reason was that even if I wanted to compare those two languages, it felt soon too tiresome to swith between those two. Also the Ziglings exercises felt smaller and more fast based than Rustlings and it was easier to get hooked by them since there was constant feeling of progress.

One noticeable difference between Ziglings and Rustlings is, that Ziglings aims to have all guidance built in to the exercises, while Rustlings heavily leans towards Rust Book and those two should be used parallel in order to understand Rust concepts well enough. I felt it refreshing and appealing to have everything needed in exercise files as guidance.

The exercises themselves were mostly easy and small in a sense that only couple of missing places had to be filled. So not much typing required. Also answers could be mostly drawn directly from the description. Some needed more thinking and understanding before finding the correct solution. Zig compiler is somewhat helpfull on finding what's wrong and in some cases using other means such as Google or ChatGPT was needed. Copilot I did turn off before doing the exercises in order to not let it spoil my mental work with right answers.

Overall it took me couple of weeks go through all the (around 100) exercises. I think they were very nicely put together. They handle lot's of areas on the language. I feel that I understand the language concepts now much better and am actually quite eager to learn more. Also I feel a bit sad, that now it's done - there was certainly something very rewarding once I was able to accomplish my daily Ziglings session.

## Conclusions

I can very much recommend Ziglings as a learning tool for Zig or low level programming languages in general. Exercises are well formulated and fun to solve.

I don't know yet if Zig is the language for me and further work and comparison is needed, but at least I can say that there is lot I like in the language and maybe more importantly nothing is pushing me away from me it at the moment.

It will be interesting to follow how Zig grows to gain 1.0 Version at some point. I surely think it has a potential of being my "language-of-choise" for more performance critical applications. Meanwhile I will continue my journey with Rust and also considering Nim as a third option.
