---
title: 'Bytesize: Configuring lint tests'
subtitle: Phil Ewels, Seqera labs
type: talk
start_date: '2022-11-08'
start_time: '13:00 CET'
end_date: '2022-11-08'
end_time: '13:30 CET'
youtube_embed: https://www.youtube.com/watch?v=DiXh3Dvpq5E
location_url:
  - https://doi.org/10.6084/m9.figshare.21533151.v1
  - https://www.youtube.com/watch?v=DiXh3Dvpq5E
---

# nf-core/bytesize

Join us for our **weekly series** of short talks: **“nf-core/bytesize”**.

Just **15 minutes** + questions, we will be focussing on topics about using and developing nf-core pipelines.
These will be recorded and made available at <https://nf-co.re>
It is our hope that these talks / videos will build an archive of training material that can complement our documentation. Got an idea for a talk? Let us know on the [`#bytesize`](https://nfcore.slack.com/channels/bytesize) Slack channel!

## Bytesize: Configuring lint tests

This week, Phil Ewels ([@ewels](https://github.com/ewels)) will show how to configure lint tests.

<details markdown="1"><summary>Video transcription</summary>
:::note
The content has been edited to make it reader-friendly
:::

[0:01](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=1)
Hello, everyone. Welcome to today's bytesize talk. My name is Franziska Bonath. I'm today's host. But before we go into the talk, I would like to highlight an event. As you all know, we had a hackathon not long ago, which was hugely successful. And good news, there will be another one coming up in March 2023. This one will be a completely virtual event, but we are planning to have local communities come together and join forces. So if you want to host one of those, please contact us or add a pull request to the website where you can add your own site.
So, this is enough of announcements. Now I would like to hand over to Phil, who's giving today's talk. He is talking about configuring linting.

[1:07](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=67)
This is me stepping in, doing a bit of a last minute talk again. So sorry for the late advertisement about this one, and about what the talk topic would be. We realized, actually as Fran had been telling us for a couple of weeks, that we were running out of talks, but we only really realized yesterday that we didn't have anything scheduled for today. So we had a look through a list of suggestions, and this was one which had been asked - or requested - a couple of times. So I thought I'd jump in and try and talk everyone through nf-core linting. What it is, how it works, but most importantly, how to configure it.

[1:40](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=100)
This is going to be particularly interesting for both people building nf-core pipelines, but also people working with nf-core tooling outside of the nf-core ecosystem, where maybe some of these tests are not really relevant to you. But it's good for everyone to understand how it works and how to configure it. In the spirit of last minute decisions, I'm going to be live demoing everything. My apologies in advance if everything breaks horribly, but let's see how it goes.

[2:13](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=133)
Hopefully you can all see my screen. Move your faces over here. Wonderful. First things first, just a reminder about where the documentation is for all of this. So this is the nf-core website. If you head on over to the tools page up at the top here, you find the section for nf-core lint. This is the documentation. It pretty much tells you everything I'm going to tell you. If you get distracted by a dog or a neighbour or something, or if you forget everything else I'm going to tell you about, then just remember this. There is documentation on the website and you can read it and it explains how everything works. With that said, let's jump in and do some demo and walk through what we're going to be talking about today.

[3:04](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=184)
Just before we started recording, I created a new pipeline. I just did `nf-core create`, which made me a new empty pipeline. Then I updated the modules and I cleared out some of the template stuff so that we're up and running with a clean pipeline. If I do `git log`, you can see I've just got two commits from when we started preparation for this talk and just resolving the link warnings.

[3:33](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=213)
Now, what is code linting or linter? Basically, a code linter has a set of rules about how code should look and how it should work. It checks the code and gives you passes or failures in a series of tests. Typically, when you talk about code linters, you're also talking about code formatting. So, for example, you can have a JavaScript or JSON code formatter or Python code formatter. These have linting tests where they look at the code and they can also reformat the code for you. We're not talking about that today. The nf-core linter doesn't have anything to do with code formatting. It's just a set of rules and it's a way to inspect code for standardization. The linting has been around for nf-core since we started the project and is a fairly crucial part of how we work. It allows the community to scale because we do so much work with code review where other people are reviewing code that you're writing. Having a set of automated tests means that we can be a bit more confident that things are adhering to the principles and not breaking stuff without having to check really, really carefully. By automating the things that we are commonly asking for, it streamlines this process of code review and makes the general code quality better. This is pretty much how the nf-core lint tools have evolved over time. It started off with a couple of minor things. Every time we've come across the same thing a few times in a pull request, we say we should write a lint test for that so we can automatically test for this thing again in the future.

[5:10](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=310)
Another thing that's really important about it is it makes sure that everybody stays up to date with all the latest things, guidelines and rules for nf-core because these change over time. We have updates in the nf-core template and we do template synchronization, and all this stuff, and that rolls along. What happens is if you keep running nf-core lints on your code base over time, as retooling updates along with the template, you'll start to get failures where before it was passing. That's because it says it checks the code in your pipeline against the template. In some places it says this should be the same as the template. It's either out of date or you've edited it and that's a bad thing. So it also forces everyone to stay up to date and in sync. That's why we have retooling. That's why we have nf-core linting.

[5:56](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=354)
If I do `nf-core help`, you'll see that it's one of the commands here, commands for developers. The basic one is `nf-core lint`. You can also call it just specifically for modules. So it's an `nf-core modules lint` and that calls just a subset of those tests. But generally speaking, you just do `nf-core lint` and it just checks everything. I'm currently sat in my working directory as the root of the pipeline. Here it's run 182 different tests and they all passed and nothing failed. That's great! When I push commits and open pull requests to GitHub and things, we have automated continuous integration tests, zero tests, and they run the same command on the code. That means when you open a pull request, those tests automatically run and you get a green tick or red cross saying whether this is good or bad. We have different statuses that the tests can have: they can be passed, they can fail (which obviously is bad) and they can throw warnings. With warnings, they don't actually fail the CI, so you'll still get a green tick. But it tells you in the text message when you check the text that there's something which is maybe not ideal. Hopefully when you're creating pull requests, you also get a little comment automatically added, which summarizes the results in the body of the pull request. To give you visibility, especially for these warnings, which might fly under the radar otherwise.

[7:15](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=435)
What we're particularly interested about today is this category of tests, which are ignored. I'm going to come on and tell you how to ignore CI tests. But first, let's make something fail. So if I open up VS Code here, this is my pipeline that I've just created. I'm going to dig in as different, lots of different tests which I could make fail. But I'm going to start off by doing something very simple. We've got the readme file here. One of the tests checks that the nf-core readmes look like nf-core readmes. All nf-core readmes have these badges at the top. I'll show you what I mean if I go to RNA-Seq. Go to github.com and look at the main readme on when you load up the repo. It's got these badges along the top, which says it works with Nextflow and you need this version or whatever. We have a lint test that checks these badges are there and correctly formatted and consistent. For example, it says the minimum version of Nextflow you need. You also define that in the config file down here. It's quite easy to let these two fall out of sync. We have a lint test which checks this button in the readme. Simple. So I'm going to break it by deleting those. It's gone. The markdown file is still totally valid. It just doesn't have any of the buttons at the top.

[8:32](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=512)
OK, so proof of principle, nf-core lint now should hopefully fail. OK, it didn't fail. It gave us a warning. Close enough. Really important about this is, when you see these warnings, you get a summary text saying didn't have a Nextflow minimum version badge. But it might be that you don't completely understand what the message is or it's a bit unclear. Most browsers will handle these hyperlinks in the terminal and where it says readme, that's the identifier for this test. If I hold, I'm on a Mac, so if I hold command, I think on Linux and Windows, it's control. I can click that and it's going to open up a tab in my web browser and it's going to go specifically to the test ID called "readme". This is where we have longer form documentation about this specific lint test. Here you can see it says it needs to have a Nextflow badge and it should look like this and it should be the right thing. It should have a bioconda badge and everything. So this is where the long form documentation about these lint tests are. You can also find it if you go to tools and then somewhere under tools. I always forget. Anyway, you follow that link here and it tells you all about it.

[9:49](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=589)
I noticed that this is quite specific, it's not just editing the whole file, but it's just this part of the readme file is checked and there are other ones as well. If I go into, let me see, assets, multiqc.config, do `foo: bar`, then I should get, I think, a failure about editing the multiqc file. And... no. OK, I managed to do something right there. But anyway, there's certain files where you can get a failure for putting stuff at the start, but if you stick it at the end, it will be valid and things. That test is, I think, called "files unchanged" here. So I should have picked one which is actually tested. These ones, you can add extra stuff and see - the gitignore file, for example. So, yeah, these are all different lint tests and they're documented here.

[10:51](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=651)
Obviously I could fix this readme file by putting the badges back in, rerunning a template, etc, or reading the documentation and seeing what's required. But in this case, maybe I don't want to do that. Maybe I'm building outside of nf-core and I don't want to have the nf-core badges at the top and I want to do my own thing. That's fine. How do I go about doing that? I need to ignore this test! The way I do that is when you run `nf-core create`, you get a config file for nf-core, this one, ".nf-core.yml", which is a hidden file. Depending on how you're looking at your files, you may need to show hidden files. In here, by default, we just have this. It just says this repository is a pipeline. This has to do with working with modules. But I can add a new key in here called "lint". Under "lint", I'm going to give the name of the test that failed, which is called "readme". I'm going to set it to false. I'm going to hit save. That's what it is. I'm going to rerun linting now. Now it says pipeline test ignored. It just says it didn't run this test. Because of that, nothing failed and everything's fine. That's basically all there is to it.

[12:03](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=723)
If there are any of these lint tests you don't like that you are sure that it's doing what you want it to be doing, then you can just say ignore this test and it will be ignored. This is quite a blunt tool. I've just ignored this entire lint test called "readme". For example, if I go in and I change... let's do "files exist" here. So I'm going to delete the editor config file. `nf-core lint`. Then it should throw a failure because this is a required file and it's not there. So it takes me here. Now I could do `files_exist: false` and that will disable the entire lint test. That's fine. That makes everything work. But it's a bit of a blunt tool because now it's not checking for the presence of any files. So it's allowing me to delete that one file, but it's now not checking for the presence of any of these files, which is maybe overkill because it was just that one file I care about. Now, some of the lint tests then allow you to provide a bit more information. In this case, what was the file I deleted? Editor config. Instead of just saying "false", I can actually give the name of the file there. I think it has to be a list. Now, when I run this again, hopefully it should still pass. But now it ignores just that one specific file. So this isn't possible for every single one nf-core lint tests, you have to check the documentation. But certainly "files_exist" and "files_unchanged", which are probably two of the ones which come up the most frequently.

[13:54](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=834)
You can specify exactly which files you want to ignore and then you keep the value of the rest of the lint test there, which checks all the other stuff, which is generally a good thing. Right. 13.14, that's my 15 minutes. It's a very short and sharp bytesize talk this time. Very specific about this one thing. But hopefully that's helpful. Hopefully this will be a useful resource for anyone coming back to this in the future, asking about how lint configs work. If you have any questions or feedback or suggestions, then please shout and I'll do my best to answer any questions if there are any now. Fran back to you.

[14:33](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=873)
(host) Yeah. Hi. So everyone can unmute themselves if they want to and just ask the question right away. I don't see anything coming up for now. I mean, you don't have to ask now. Obviously, as usual, you can come to Slack, either ask your questions in #bytesize or in the #help. As usual, I would like to thank the Chan Zuckerberg Initiative for funding the talks, of course, Phil, for this very-short-notice talk today and the audience for listening.

[15:12](https://www.youtube.com/watch?v=DiXh3Dvpq5E&t=912)
Also, just say on Slack that there's a Slack channel called Linting. I think it's called Linting, which is the place to go for any specific questions about Linting when you're generally confused.
(host) Yeah, I should have known there's always a Slack channel for everything. Thank you, Phil.
Always.

</details>