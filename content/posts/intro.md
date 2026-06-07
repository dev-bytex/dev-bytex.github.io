+++
title = "My First Blog Post"
date = "2026-06-07"
+++

# Why I'm Learning Zig Alongside Rust

<img src="https://ziglang.org/img/Ziggy_7.svg" alt="My Diagram" style="width: 40%; max-width: 400px; display: block; margin: 0 auto;" />


I have been learning Rust for a while now and the journey has been really fun for me. I really love how the language itself is built, from the type system, the syntax, the compiler error messages and strictness. I love how Rust has helped me become a better engineer. 

But, with all these, why do I still want to learn Zig? Honestly, I love building things from scratch. Rust lets me do this, but the moment I need to get close to the metal – managing memory precisely, understanding what the CPU is doing – the abstractions start fighting me. Also, I could just have used C, which also gives you full control but comes with some footguns. So, that is why I decided to learn Zig.

I discovered Zig while researching languages that would give me full low-level control. It clicked when I watched Andrew Kelley being interviewed by JetBrains – hearing his vision for the language and the philosophy behind it made everything make sense. 

Zig also gives you full control of your computer. It is even said to be closer to the metal than C. Learning Zig will teach you more about how computer systems work under the hood because most things other languages handle invisibly – memory allocation, knowing what owns what, how allocations work – you handle them explicitly in Zig. That explicitness is the main point. 

Zig is also easier to read than C or C++. There is no hidden control flow, no operator overloading, no implicit anything. What you see is exactly what runs – which matters a lot in a large codebase where you are reading more code than you are writing. 

One of Zig’s powerful features is `comptime` – the ability to run code at compile time instead of run time. Instead of a separate macro system like Rust’s `macro_rules!` or proc macros, you write regular Zig code and mark it `comptime`. The compiler evaluates it before your program runs – generating code, calculating values, inspecting types. It replaces an entire category of metaprogramming complexity with something that just reads like normal code.

Zig also compiles C code directly – no separate C compiler needed. This means every C library ever written is available to your Zig code with minimal friction. Given that most systems software is written in C, this matters more than it might initially seem.

I created this blog for two reasons. First, to solidify my own understanding – writing forces clarity that reading never does. Second, because the Zig ecosystem needs more accessible content. Most Zig material assumes you already know C deeply. I want to change that – writing content that makes Zig accessible whether you are coming from a high-level language or just starting out. 

Whether you are coming from Rust, Python, JavaScript, or any other language or if you are just starting your programming journey – if you are curious about how computers work at the fundamental level or just want to learn computer science in general, this is for you.
The first blog post goes up Tuesday – we are starting with implementing the Merge Sort algorithm in Zig with some visual examples and real-life applications. See you there.

