# Building a FOSS Niagara-like Android Launcher: A Postmortem

## Origins and Motivation

My college class had a straightforward requirement: build a mobile or web app over one semester. Four quarters—design, data implementation, feature development, polish.

I chose Android, and the decision wasn't arbitrary.

Web development has never appealed to me. The ecosystem is messy. JavaScript is slow and underspecified as a language. Web apps are bloated, and the ones I've used leave me wanting native solutions. This sentiment isn't theoretical—it's born from experience with a 2GB RAM 2014 Celeron laptop. Bulky, no battery, faulty peripherals, slow performance. I'd moved to Linux once Windows 7 was no longer needed for college projects, and on that hardware, native solutions weren't just preferable—they were the only tolerable option. That Celeron is where my minimalist native ethos came from.

I've since upgraded to an MSI Bravo B7ED with 8GB RAM. RAM is still a pain point, but it's manageable now.

Android also had a practical angle. A friend's relative might hire for an app dev project. Learning Android felt like preparation—if the opportunity arose, I'd be ready. I'd briefly tried Android dev half a decade prior, so I had surface-level familiarity with Kotlin and Android Studio. Not expertise, but enough to not be starting from absolute zero.

## Finding the Project

I needed an idea. Something feasible, something I cared about enough to work on for months.

I was pondering when I found what seemed like the perfect candidate: **a FOSS Niagara launcher clone**.

[Niagara Launcher](https://www.niagaralauncher.app/) is a proprietary Android launcher with a specific design philosophy. Minimal interface. Alphabetical app list. Minimal widgets. Fast workflow. It's opinionated and it works. But it's closed-source paywalled, and while perusing forums online, I discovered there were no FOSS alternatives that matched its approach.

Here was the kicker: it seemed small in scope. So small that I feared it wouldn't be enough for a semester-long project. I openly stated in my pitch that it would take about two weeks, maybe three. The real concern wasn't whether I could finish—it was whether I'd have *enough* to show for a semester's work.

In hindsight: I was wrong.

## Technology Decisions

### Flutter vs Native Android

[Flutter](https://flutter.dev/) was tempting. It's a higher-level framework with tons of plugins and extensions. Cross-platform by design. I'd seen it work impressively—[Grim Quest](https://play.google.com/store/apps/details?id=com.grimdev.grimquest), a game I'd played, was entirely written in Flutter by a solo developer. If one person could ship a complete game in Flutter, surely a launcher was feasible.

But after consideration, my particular project needed close access to Android-specific facilities. Flutter's platform-agnostic nature would require extensive glue code via [platform channels](https://docs.flutter.dev/platform-integration/platform-channels)—basically writing Java or Kotlin anyway to bridge Flutter to Android APIs. My latest data-streaming iteration would've been fine with that approach, but overall, Jetpack Compose is faster and native. And when making this type of application, the general sentiment among Android devs was indeed to go with native Android tooling.

### Views vs Jetpack Compose

Here's where another issue cropped up.

My prior adventures with Android dev used the **Android Views system**. Classic, XML-based layouts with Java/Kotlin code. Imperative UI. But the "idiomatic" way nowadays is [Jetpack Compose](https://developer.android.com/jetpack/compose), a declarative UI framework that's code-first, even for UI. Much like Flutter in philosophy, but native to Android.

Yes, you can still make apps using the Views system, but it doesn't have first-class support anymore. The ecosystem has moved on. I'd pretty much settled on Jetpack Compose.

And here's my next problem.

Since I was pretty new to this, a tutorial for "making your own Android launcher" would've been nice to go off of. But tutorials and docs for this were scarce, antiquated, and mostly based on the Views system. There were FOSS codebases for launchers in Jetpack Compose + Kotlin, but I wasn't motivated to read them. Instead, I opted to just do it myself.

## Getting Started Was the Hardest Part

I wanted to make a launcher that was fullscreen and displayed the system wallpaper.

This proved difficult. Surprisingly, frustratingly difficult. So much so that I prototyped the app in Views first [based on a tutorial](https://medium.com/@KunalRaghav/building-yal-yet-another-launcher-a-rudimentary-app-launcher-1b3164c2f473), then migrated it to Jetpack Compose. Only once I had that basic setup—a fullscreen activity showing the wallpaper—could I finally get started doing the actual work of learning.

### The Design Phase (Or Lack Thereof)

For the project, we were instructed to come up with a clear design or vision. I didn't take this too seriously at the time because I was sort of cloning an existing product. I felt some things were obvious, left unsaid.

But here's the thing: even if I'd *wanted* to take it seriously, my lack of domain knowledge meant I couldn't come up with a realistic design that would be simple enough to produce. I didn't know what was hard and what was easy. I didn't know what Jetpack Compose made trivial and what it made painful. I didn't know the architecture patterns, the data flow idioms, the gotchas.

This technically didn't matter in the end because dozens of iterations molded the design anyway. But it made the early phase chaotic—I was learning the domain *and* trying to build in it simultaneously.

## The Learning Process

I was new to GUI programming of this nature in general. I'd mostly stuck to CLI programs, games, or Java Swing/C# WinForms (recently), which are more forgiving and direct. Jetpack Compose was a new beast. Declarative UI, state management, recomposition—concepts I'd heard about but never internalized.

The learning process was rough, to say the least.

Basically, at each point I had a vision of what I wanted and grinded out code to make it happen. Then I'd learn something new—a better pattern, a more idiomatic approach, a limitation I hadn't anticipated—and I'd scrap the old codebase and start anew. I never really sat down and read all of the Jetpack Compose or Kotlin docs cover-to-cover. I don't know if doing so would've made me more productive and efficient, or if it would've turned me off out of boredom. So I stuck with iteration.

I tried for rapid iteration, but the tooling was horrendously bloated.

Compilation was slow. So slow that at times I'd be watching videos or playing games as I [waited for the build to finish.](https://xkcd.com/303/) Android Studio on 8GB RAM is a resource hog. I configured memory settings—reduced all values where they were higher or uncapped, because if it can eat RAM, it will—and that prevented frequent crashing. But the slowness remained. Every change, every rebuild, every deployment to a device or emulator took time. It ground down my momentum.

## The Five Major Scraps

I scrapped the project and reset about five major times.

Each time, something fundamental changed:
- The architecture
- The data model
- The UI structure
- My understanding of Jetpack Compose and Android's idioms

My growing domain knowledge kept sparking that rewrite itch. And I fully succumbed to it, prioritizing rapid iteration over painstaking upfront design. Maybe that was the right call given my inexperience. Maybe it wasn't. But it's what happened.

### Iteration Four: Something Presentable

By the fourth iteration, I had something substantial and presentable. About 80% of my original pitch was implemented and working. I wasn't stressed about being empty-handed for the presentation anymore.

The architecture was MVC-esque with a singular data controller point and an event system. Very custom. Very reinvent-the-wheel-y. Persistence was through [SharedPreferences](https://developer.android.com/training/data-storage/shared-preferences), which I'd known about way back when.

But I wasn't satisfied with the codebase. It worked, but it felt brittle. Scaling it or adding features felt like it would be painful.

So out of genuine interest—not panic, not a deadline—I finally looked at a few docs about Jetpack Compose technologies. And what I discovered was:
- [MVVM architecture](https://developer.android.com/topic/architecture)
- [Jetpack Navigation](https://developer.android.com/jetpack/compose/navigation)
- [Proto DataStore](https://developer.android.com/topic/libraries/architecture/datastore)

Hell, this was exactly what I needed.

### Iteration Five: The Final Architecture

The fifth and final scrap was born. The current incarnation of the project.

With my knowledge of both the pros and pain points of Jetpack Compose—mainly the ergonomics of architecture and how to define data properly—my project design took another turn. It became less Niagara-like and more standard. But it was more sane, more scalable, and easier to keep a mental model of.

**Why these technologies solved my problems:**
- **MVVM:** Gave me clear separation of concerns and reduced the boilerplate I'd been reinventing myself. ViewModels handle UI state, the UI layer just reacts.
- **Jetpack Navigation:** Proper routing with type safety instead of my hacky manual navigation system. The navigation graph made it easy to reason about app flow.
- **Proto DataStore:** Modern, type-safe, asynchronous. Plain better than SharedPreferences in every way.

This time it was like new ground again. Each of the new technologies had quirks and usage patterns that meant I couldn't really reuse any of my old code and had to start fresh. But it was guided. Disciplined. Informed by the final architectural decisions I'd made.

I was once again on my way to a stable, presentable state, and this time I felt it was final. I was actually up to date with modern support and idiomatic practices. In fact, there were a few libraries I could've used for certain features, but I wrote minimal implementations myself since my requirements were simple.

Everything was smooth sailing.

## Daily Driving the Launcher

I had a working launcher that I genuinely daily drove on my own phone for weeks as I developed the app.

Using your own software is clarifying. You notice the friction points immediately. The features that seem clever in your head but are annoying in practice. The gestures that don't feel natural. The performance hiccups that accumulate into frustration.

It made the development loop tighter and more honest.

I even added some of the original stretch goals during this phase: lock screen functionality and UI configurability.

## The Final Feature Set

The 0.1.0 build had:

- **Widgets view:** A scrolling list of widgets. Minimal, customizable.
- **App list view:** Scrolling, searchable, alphabetical list of installed apps.
- **Floating action button:** Tap to jump to app search. Long-press to open settings.
- **Context menu:** Long-press an app icon to go to its settings page.
- **Settings:** Organized into general, apps, widgets, system, and experimental categories.
- **Lock functionality:** Double-tap the widgets view or alphabet scroll to lock the device.
- **Persistence:** All data saved via Proto DataStore.
- **Material You design:** Wallpaper-adaptive colors, transparency effects.

Under the hood, the data flow had evolved. First few iterations were standard: need data? Request it, wait for response, update UI. Later, I invented a ViewModel cache to preemptively preload views based on a smart navigation graph prediction. This increased performance and responsiveness. That's the architecture in the current 0.1.0 iteration.

## Release Hell

Submission was nearing. I pushed for release.

I generated a [release signing key](https://developer.android.com/studio/publish/app-signing) and built the app.

More problems ensued.

The release-signed release build was literally less instant and responsive compared to the debug-signed one. Same code, same logic, same build variant! Only the cryptographic signing key changed. This is bonkers to me and ticks me off to this day—it's a genuine black box problem. Behavior that's incomprehensible and out of my control due to the high-level, abstracted nature of the tooling. I couldn't debug it. I couldn't reason about it. It just *was*.

Debug-signed apps can only be installed via Android Studio and maybe ADB (I haven't tested). That's a deal breaker for normal user adoptability. So to this date, the release build has a minor performance hitch, and I couldn't fix it in time.

This, among other tiny annoyances I can't quite recall, made the Android tooling and ecosystem hell to troubleshoot and develop in.

But I built the 0.1.0 app and felt fine. It was done. Presentable. Functional.

## Current State and Reflection

The app is unpublished. I'm not planning to release it. I won't maintain it or audit the code for a proper release. I can't afford to currently, and honestly, I don't want to.

### Takeaway One: Hardware Matters

I don't have the hardware to do this without constant headaches. Android Studio on 8GB RAM is painful. On 2GB it was nearly impossible. That's the most important requirement for this kind of work, and I didn't meet it comfortably.

### Takeaway Two: Pure Mobile Dev Isn't Worth It

The experience of developing a truly minimal-scope app—something I thought would take two weeks—taking this much code, effort, and pain, coupled with the fact that Android app sales for devs aren't impressive (various YouTubers who've shipped hundreds of apps have discussed this), made pure Android dev seem economically worthless. Cross-platform is still fine, but Android-only feels like a losing proposition unless you're already established.

### Takeaway Three: I Don't Want to Do This Professionally

I learned enough about Android dev today to know I don't want to do it professionally. Perhaps even if the opportunity with my friend's relative arose, I'd decline. That's my sentiment after the hair-pulling troubleshooting and dev work.

### Takeaway Four: I'm Proud of What I Learned

I'm proud of having learned a new technology and an entire UI ideology in itself. Jetpack Compose taught me declarative UI programming—something I'd consumed in tech media as an outsider, always having dev musings about how it would work, without actually trying it.

So now I tried it. And it's not for me.

But I realize that rushing into it and learning the thing without guidance was a large part of the frustration. My recent learning experiences have been in a class setting and went smoothly with maybe one hiccup. Structure matters. Guidance matters.

### Takeaway Five: It Was Fun

For a while, as a recreational programming session, it was fun.

Then it wasn't.

Then it was done.
