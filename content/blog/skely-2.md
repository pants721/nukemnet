+++
title = "My software used to suck"
date = 2024-03-11
[taxonomies]
tags=["development", "Rust", "Skely"]
categories=["development"]
+++
 
Hi everyone! For the past week or so I've been revisiting an old project of
mine called [Skely](https://github.com/pants721/skely), which is a tool I wrote
to help use and manage project templates (a.k.a. skeletons). The last time I
touched the project was right over a year ago, around when I first started
learning rust, so the code quality was not great. Since then, I've become an
much much better developer and I want to look at some of my old code, look at
the new code, and analyze why the new code is better. This won't be a complete
code review, so if you're interested in the project's development make sure to
check it out on [Github](https://github.com/pants721/skely)!
 
The major issue with the 1.0 was over-engineering. On paper, Skely should be a
pretty simple project. It only has 3 jobs:
 
1. Copy files to its `skeletons` directory
2. Copy files from its `skeletons` directory
3. Find and replace the placeholder phrase throughout the project
 
However, Skely 1.0 drastically overachieved (or attempted to), which was the
root of the design issues. It defined its own `.sk` file format for single file
skeletons, which was utterly pointless as no kind of meta-data akin to markdown
front-matter was stored in the file what so ever. The `$ sk new` command had a
`--touch` boolean flag which just created a `.sk` file of that name in the
`skeletons` directory- again, utterly pointless.
 
Skely 1.0 also included the `edit` command. I think that this feature, although 
unnecessary and now removed, is something that I'm going to consider adding 
again. The main problem is that I was a noob and didn't know about environment 
variables. On most UNIX systems the `$EDITOR` and `$VISUAL` variables are set, 
usually to nano or vi by default. I didn't know this at the time being a 
beginner and a VSCode/Emacs Mac user, so I created this monstrosity:
```rs
pub fn open_editor(arg: &PathBuf, editor_opt: &Option<String>) -> Result<()> {
    match editor_opt {
        Some(editor) => { ... }
        None => {
            #[rustfmt::skip]
            // Editors (in order)
            let editors = vec![
                "nvim",
                "hx", // helix
                "vim",
                "micro",
                "nano",
                "emacs",
                "vi",
                "pico",
                "amp",
                "ne", // nice editor :)
            ];
 
            for editor in editors {
                let output = Command::new("which")
                    .arg(editor)
                    .output()
                    .context("Failed to execute command")?;
 
                if output.status.success() {
                    Command::new(editor)
                        .arg(arg)
                        .spawn()?
                        .wait()?;
                    break;
                }
            }
        }
    }
    Ok(())
}
```
Oh no... I hard coded default editors. This design decision is just insane 
looking back. To explain the code, when using `$ sk edit`, if an editor wasn't 
specified in Skely's `config.toml` file, then Skely would iterate over this 
hard coded list of editors in order and find the first one where the `$ which 
<EDITOR>` command succeeded.
 
If you haven't realized already, this is an INSANE way to do this. First of 
all, what happens if none of these editors are installed? Is the command just 
non-functional then? Second, why is this the order? Most users aren't going to 
read the configuration docs, so what if a Emacs user has Neovim installed on 
their system? Are they just going to assume Skely is only compatible with 
Neovim? Third, you have to edit a file if you want to change what editor you're 
using? Now, this one is slightly less egregious as world-renowned tools like 
git store preferred editor in a config file as well. However, git also supports 
environment variables so that the editor for git operations can be changed 
quickly on the fly.
 
So how would/will this be implemented better? The reason I wrote this 
horrendous code is because I didn't know how to fallback from an editor not 
being specified in the config file, but now I do: environment variables. The 
solution would be to find a user's preferred editor in this order:
1. Check `~/$XDG_CONFIG_HOME/sk/config.toml`
2. Use `$SK_EDITOR` environment variable
3. Use `$EDITOR` environment variable
4. Use `$VISUAL` environment variable
5. vi
6. How are you even using a computer if none of those work
 
At my current skill level, this would be the best way to find an editor. The 
implementation would look something like this:
```rs
fn get_editor(&self) -> Result<String> {
    // Redundant, but this is just an example
    if let Ok(editor) = self.settings.editor {
        return editor
    }
 
    if let Ok(editor) = env::var("SK_EDITOR") {
        return editor;
    }
 
    if let Ok(editor) = env::var("EDITOR") {
        return editor;
    }
 
    if let Ok(editor) = env::var("VISUAL") {
        return editor;
    }
 
    "vi".into()
}
```
 
Now, let's talk about project structure. Skely 1.0 had way too many files for 
how simple of a project it was. The original file tree looked like this:
```bash
src
├── app.rs
├── cli.rs
├── common.rs
├── main.rs
├── settings.rs
└── skeleton.rs
```
This is wayyyy too many files for such a simple project. In any language, the 
purpose of having multiple source files is to split up code logically, to allow 
for visibility of code to specified as public or private, and to be able 
extract, reuse, and reference pieces of code. So if Skely were a large growing 
project that needed lots of scalability, this organization would make sense 
(except for `common.rs`, but we'll get to that). The problem goes back to 
over-engineering. Skely doesn't do a lot, so this level of abstraction, 
although not criminal, doesn't make lots of sense to me. For example, let's 
look at `settings.rs`:
```rs
// settings.rs
 
#[derive(Serialize, Deserialize, Debug)]
pub struct Settings {
    pub editor: Option<String>,
    pub placeholder: Option<String>,
}
 
impl Settings {
    ...
}
```
This struct stores a preferred editor and a placeholder phrase. Although I've 
since removed this struct from the source code, it's functionality makes sense, 
especially in the context of serializing and deserializing with 
[serde](https://serde.rs/). But when would this ever be used outside of the 
`App` struct that stores the central data for Skely? One could argue that you 
might pass it into a helper function as a type, but I would argue that a helper 
function that needs this should either be implemented for `App` or should 
accept the values of the settings it needs to know about. So because this code 
has no real use outside of the `App` struct, why not store it in `app.rs`? In a 
project where there are multiple variations of settings for different purposes, 
or if Skely implemented per-project settings, extracting `Settings` would make 
sense again. For the former, I'd go with a trait of some sort, and for the 
latter I'd use the same struct. As of now, Skely doesn't use a config file and 
instead relies on the `$SK_PLACEHOLDER` environment variable and doesn't have 
an edit feature.
 
Another funny part of Skely 1.0 is the existence of a `common.rs` file. If I 
remember correctly, I used this just because I had seen it as a convention in 
smaller C projects, but looking back now it was just lazy coding. During my 
initial attempts to refactor the project last week I separated it into 
`cli_util.rs` and `file_util.rs` respectively. In Skely 2.0, I've just 
flattened it into one `util.rs` file that contains basic file manipulation 
utilities, which I think is fine as they aren't completely miscellaneous and 
all take non-Skely data and return non-Skely data.
 
There are many other issues with Skely 1.0, but this post is getting slightly 
long-winded and preachy, especially for a young, learning, and growing 
developer like myself. I think that it's very good to be able to go over my old 
code and talk about what I did poorly and what I've learned from it. I also 
have no doubt that I will be writing another post like this about code I'm 
writing now in a year or two. But that's okay, because growing is okay and 
mistakes are okay.
 
Same as your code, you are also a growing and ever-improving project. I hope 
you keep that in mind. Give yourself some grace; let yourself breathe.
 
-Lucas
