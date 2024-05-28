# Install and run Sparv

Install Sparv on your computer if you haven't already done so. Follow the
[installation instructions in the manual](https://spraakbanken.gu.se/sparv/#/user-manual/quick-start).

Complete the whole quick start guide (except the *What's Next?* chapter), to get familiar with creating a corpus and a
config file, and to make sure that Sparv works.

> [!IMPORTANT]
> Remember to always use UTF-8 when saving any YAML, Python or XML files during these exercises.

# Download the example corpus

Download the [example corpus](https://github.com/spraakbanken/sparv-retreat2024/raw/main/djurkorpus.zip).
This is the corpus we will use to test the Sparv plugin we will create.
Create a new corpus folder and unzip the example corpus there. Your corpus folder should now look like this
(but probably with another name):

```
djurkorpus
├── config.yaml
└── source
    ├── igelkottar.xml
    ├── rävar.xml
    └── ugglor.xml
```

In addition to the three corpus texts, you also get a Sparv corpus configuration file.

# Get the Sparv Plugin Template (and create a repository)

To make it easier to get started with creating a Sparv plugin, we provide a template which you can use as a base to
build on.

> [!NOTE]
> If you are not comfortable using `git`, there is a section for you below.  
> However, for any real plugins you create in the future, you should use `git` and publish them on GitHub.

## Alternative 1: Using git

Begin by creating your own repository based on the template. You do this by going to
the [Sparv Plugin template](https://github.com/spraakbanken/sparv-plugin-template) repository and
clicking the green "Use this template" button, and then "Create a new repository".
When asked which account to create the repo under, **use your own account**, not Språkbanken, to not pollute our
organization with countless test plugins.

Clone your new repository to your computer. This new folder now contains the base for your plugin.

## Alternative 2: Not using git

Instead of making a new `git` repository based on the template, you can download the
template by clicking the green "Code" button and selecting "Download ZIP". Unzip this file somewhere on your computer.
This new folder now contains the base for your plugin.

## What your plugin folder should look like

```
my-plugin-folder
├── LICENSE
├── pyproject.toml
├── README.md
└── sbx_uppercase
    ├── __init__.py
    └── uppercase.py
```

`LICENSE` and `README.md` are self-explanatory. `pyproject.toml` is a configuration file with information about
the Python package that makes up your plugin. `sbx_uppercase` is a folder containing all the code of the plugin, and
is also the name that will be used to refer to the plugin in Sparv.

## The name of the plugin

The template consists of an example plugin called `sbx_uppercase`.
To not complicate things, you should keep this name during this exercise. If you absolutely do want to change it,
you have to rename the folder `sbx_uppercase`, and change all the references to the name in the files `pyproject.toml`
and `uppercase.py`.

# Get started with plugin development

Everything (hopefully) you need to know about creating a Sparv plugin
is [covered in the Sparv documentation](https://spraakbanken.gu.se/sparv/#/developers-guide/writing-sparv-plugins).
You don't have to read it all right now, but keep it open in another tab in case you need it later.

## Install your plugin

The first thing you should do is installing your plugin so Sparv knows it exists. The template is a complete plugin,
and can be installed before you add any more code.

If you installed Sparv using `pipx`, install the plugin by running the following command:

`pipx inject -e sparv-pipeline /path/to/my-plugin-folder`

If you installed Sparv using `pip`, hopefully in a virtual environment, activate that environment and install the plugin
using pip:

`pip install -e sparv-pipeline /path/to/my-plugin-folder`

By using the `-e` flag, or `--editable`, you don't have to re-install the plugin everytime you make changes to the code.
Just remember to not move or rename the folder containing your plugin after this.

## Have a look at the code

The plugin template already comes with a simple example annotator. This annotator creates a new annotation on the
token level, annotating every token with an uppercase version of itself. Open the file `uppercase.py` to look at the
code.

The `@annotator` decorator is what makes an annotator function an annotator, and is what registers it with Sparv.
What an annotator takes as input and
produces as output is declared by the signature of the function, i.e. the parameters and their type hints, using
special Sparv classes like `Annotation` and `Output`. The decorators and classes are all imported from `sparv.api`.
As you can see in the code, reading and writing the annotations involve the methods `read()` and `write()`.

You can learn more about this by reading the section [_Module Code_](https://spraakbanken.gu.se/sparv/#/developers-guide/writing-sparv-plugins?id=module-code)
in the manual.
For details about available methods on the Sparv classes, see the
[_Sparv Classes_](https://spraakbanken.gu.se/sparv/#/developers-guide/sparv-classes) section.

## Some general concepts

This is a short summary of the section [_General Concepts_](https://spraakbanken.gu.se/sparv/#/developers-guide/general-concepts)
in the manual.

### Annotations

Sparv works with two types of annotations:

- **Span annotations** hold information about spans in the source text, like the beginning and end of sentences
  or tokens. A tokenizer produces this type of annotation, for example.
- **Attribute annotations** are always attached to a span annotation, and adds additional information to these spans,
  for example part-of-speech information to tokens, or author information to documents. Most annotators are of this
  type.

You can think of span annotations as XML elements, and attribute annotations as XML attributes.

Annotations in Sparv are referred to like this: `module_name.annotation`. For example, the default tokenizer is
provided by the module named `segment` and produces the annotation `segment.token`.

_Attribute annotations_ are always attached to a span annotation, separated by a colon. For example, the Stanza module's
part-of-speech annotation attached to the previously mentioned tokens would look like this: `segment.token:stanza.pos`.

### Annotation classes

Sometimes an annotator doesn't care about exactly what other annotator produced a certain annotation. It doesn't have
to be exactly `segment.token`, since all it needs is tokenized input. For this reason Sparv gives you the option of
using _annotation classes_.
These are referred to like this: `<class_name>`, for example `<token>` or `<sentence>`. Stanza's part-of-speech
annotator doesn't care about the tokenizer used, and its output is therefore specified as `<token>:stanza.pos`.

Classes can also be used
for attribute annotations, like `<token:pos>`, which will use whatever part-of-speech tagger Sparv is configured to
use by default.

# Extend the plugin

You are now going to extend the plugin with more functionality by adding a few more annotators. A plugin can contain
any number of annotators.
It's up to you if you want to keep them all in the same Python file or separated in several
files (one file is easier). If you go for multiple files, just remember the following:

- There should only be one folder with code (possibly containing sub-folders if you want). In the template this folder
  is the `sbx_uppercase` folder.
- You have to `import` all the Python files you create in `__init__.py` for Sparv to know about them.

# Task 1: Create a simple token-based annotator

Extend the plugin with a new annotator, adding a new annotation on the token level. It is up to you what it should
do. Some examples:

- Reverse the word
- Lowercase! 🤯

> [!NOTE]
> Just like the output of the uppercase annotator (`<token>:sbx_uppercase.upper`), the name of the output needs
> to be prefixed with the name of your plugin. This is to prevent name collisions between the output of different
> plugins.

## Update your corpus configuration to use the new annotation

Now that you've added support for a new annotation in your plugin, you need to update your corpus configration file
(`config.yaml`) to tell
Sparv to use the new annotation. Add your annotation to the `export.annotations` list. In the example below, we've
added the uppercase annotation `<token>:sbx_uppercase.upper`:

```yaml
export:
  annotations:
    - <sentence>
    - <token>
    - <token>:sbx_uppercase.upper
```

In your corpus folder, run `sparv clean -e` to remove any existing previous results, and then run `sparv run`
to create new export files containing your new annotation. Check them out!

# Task 2: Create a sentence-based annotator

Add a new annotator to your plugin. This time you are going to create an annotation on the sentence level instead,
based on the part-of-speech annotation of the tokens for each sentence.

_Annotate each sentence with the number of nouns it contains._ Use the annotation class `<token:pos>` as input instead
of `<token:word>`. You also need the annotation `<sentence>`. The POS tags are by default using the SUC tag set,
so nouns are represented by the string `NN`.

Use the `get_children()` method on the sentence annotation to loop through the sentences and the words belonging to
them. To quote the documentation for `get_children()`:
> Returns two lists. The first one is a list with n (= total number of parents) elements where every element is a
> list of indices in the child annotation. The second one is a list of orphans, i.e. containing indices in the
> child annotation that have no parent.

Here is a short example of how to traverse the sentences and their children.
Below that, hidden behind the spoiler warning, is an almost complete example of an annotator, but only use
that if you really need it!
```py
sentences, orphans = sentence.get_children(token_pos)  # Ignore the orphans
token_pos_list = list(token_pos.read())  # Convert the iterator to a list to access the tokens by index
for s in sentences:
    for i in s:
        print(token_pos_list[i])  # Do something here
```

<details>
<summary>⚠️ Spoiler: Click to see an almost complete solution</summary>

```py
@annotator("Count number of nouns per sentence")
def sentence_nouns(
    token_pos: Annotation = Annotation("<token:pos>"),
    sentence: Annotation = Annotation("<sentence>"),
    out: Output = Output("<sentence>:sbx_uppercase.nouncount"),
):
    """Annotate sentences with number of nouns per sentence."""
    sentences, _orphans = sentence.get_children(token_pos)  # Ignore the orphans 😢
    pos_list = list(token_pos.read())  # Convert iterator to a list to access the tokens by index
    output = []  # Resulting annotation values, one per sentence

    # Count the nouns per sentence
    for s in sentences:
        for w in s:
            # Do something with pos_list[w] here
        output.append(count)  # Append count to result

    out.write(output)
```
</details>

When you are done, update your corpus configuration file and try your new annotation. If you want, you can also add
`<token:pos>` to the list of annotations to include. The part-of-speech annotation will be created either way, as it
is needed by your new annotator, but it won't be included in the result unless you ask for it.
Remember to run `sparv clean -e` first, so Sparv knows it needs to create new export files.

# Task 3: Add an annotator on the text level

Add another annotator, this time creating an annotation on the text level. How this works is very similar to the
sentence level annotator, but we use the annotation class `<text>` as input.

_Annotate each text with its total number of nouns_, not by counting tokens again, but by using the output of your
previous sentence noun counter annotation as input.

> [!IMPORTANT]
> - All annotation values are currently saved as strings. When you read your previous noun counts,
>   you must convert them to integers before adding them together.
> 
> - Don't try to directly call the function you made in the previous exercise.
>   Annotators should never be called from your code.
>   Instead, use its output as input in your new annotator, and Sparv will take care of the rest.

<details>
<summary>️⚠️ Spoiler: Click to see an almost complete solution</summary>

```py
@annotator("Number of nouns per text")
def text_nouns(
    sentence_poscount: Annotation = Annotation("<sentence>:sbx_uppercase.nouncount"),
    text: Annotation = Annotation("<text>"),
    out: Output = Output("<text>:sbx_uppercase.nouncount"),
):
    """Annotate texts with number of nouns per text."""
    texts, _orphans = text.get_children(sentence_poscount)  # Ignore the orphans again 😭
    sentence_poscount_list = list(sentence_poscount.read())  # Convert iterator to a list to access the tokens by index
    output = []  # Resulting annotation values, one per text

    for t in texts:
        # Calculate sum of nouns per text here

    out.write(output)
```
</details>

Now try your new annotator, just like before!

# Bonus task: Add an exporter

Sparv supports a lot of different export formats. Usually they contain the text of the corpus and its annotations, like
the XML files we've been creating so far. But Sparv also creates CSV files with
statistics, SQL files and configuration files for Korp, and could be extended to create anything you want.
You could for example add support for an exporter which trains a language model based on corpus data.

In this task you will add your own exporter. It should create a single output file, based on data from all your source
documents.
The exported file should be a text file with two tab-separated columns.
Column one should be the name of the source document (without its file extension),
and column two should be the total number of nouns for that document. Use the output of task 3 as input.

This one is a little harder, and there is no solution to peek at this time. You will need to use several new Sparv
classes, which you can read about in the manual. The relevant classes are listed below. (Python classes,
not annotation classes!)

- Use [`AllSourceFilenames`](https://spraakbanken.gu.se/sparv/#/developers-guide/sparv-classes?id=allsourcefilenames)
  as input to get a list of all the source files.
- Use [`AnnotationAllSourceFiles`](https://spraakbanken.gu.se/sparv/#/developers-guide/sparv-classes?id=annotationallsourcefiles)
  to get your noun count for every source file.
- Use [`Export`](https://spraakbanken.gu.se/sparv/#/developers-guide/sparv-classes?id=export) for your output.
  Export files are not written using any special Sparv methods, but are instead created using regular Python methods.

# How do I create something actually useful?

The point of the above exercises was to show you the basics of how a Sparv plugin works, with its decorators, classes,
class methods and utility functions. Other than that, its just regular Python code.
If you have an external utility you want to plug in to Sparv, the plugin code will basically be a wrapper of that
utility. The plugin will handle the preparation of the input,
communicate with the external utility (using Sparv's utility functions like
[`util.system.call_binary()`](https://spraakbanken.gu.se/sparv/#/developers-guide/utilities?id=system-utils)), and then
receive the output and save it in Sparv's annotation format.
Some examples of Sparv wrappers of external tools are the
[hunpos](https://github.com/spraakbanken/sparv-pipeline/blob/master/sparv/modules/hunpos/hunpos.py),
[malt](https://github.com/spraakbanken/sparv-pipeline/blob/master/sparv/modules/malt/malt.py), and
[wsd](https://github.com/spraakbanken/sparv-pipeline/blob/master/sparv/modules/wsd/wsd.py) modules.
(These are not plugins but built-in Sparv modules, but other than being shipped with Sparv there is no difference
between a module and a plugin.)

## Some best practices to remember when developing a plugin

- All input and output in annotators should ideally be handled by Sparv and its utility functions.
- If you need to call an external program, use the utility functions provided by Sparv.
- Don't use `print()`. Use [Sparv's logging feature](https://spraakbanken.gu.se/sparv/#/developers-guide/writing-sparv-plugins?id=logging),
  and only log important information. Ideally nothing should be shown
  to the user except warnings and errors. If your annotator calls a chatty external process, silence all of its
  unnecessary output.
