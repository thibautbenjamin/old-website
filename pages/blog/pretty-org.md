
# Table of Contents

1.  [I am writing a nice document in org-mode](#org25e0902)
    1.  [Using the configurability of org mode](#orgb27e2e8)
        1.  [First step change the indentation](#org8113f62)
        2.  [Configure org-superstar mode](#org3dea552)
        3.  [Hide the todo keyword](#org7deb661)
        4.  [Add a face for the leading bullet](#org1591cde)
2.  [Putting everything together](#orga044bc1)
3.  [What is left to do?](#orgfbf177e)
    1.  [Make org-mode prettier <code>[2/5]</code>](#org39f4f9f)
        1.  [Set custom faces for the todo-bullets in org-superstar mode <code>[3/3]</code>](#org200230c)
    2.  [Additional ideas](#orgae6d75b)



<a id="org25e0902"></a>

# I am writing a nice document in org-mode

**Abstract:** Maybe this is a bit weird, but I like having a nice interface when I write and I don't really like being forced to write in terrible looking text editor, but I really enjoy the capabilities of org-mode. This configuration is inspired by other configurations that I found on the net and thought were good, in particular  [this one](https://www.reddit.com/r/emacs/comments/iemo44/wysiwygified_org_mode/), and also to some extent [this one](https://www.reddit.com/r/orgmode/comments/ikhjha/ricing_up_orgmode_completely_substitute_todo/).


<a id="orgb27e2e8"></a>

## Using the configurability of org mode

I am writing this document to display the recent configuration of org-mode that I made and explain how I achieved it. I don't have much experience with emacs configuration (yet), but I am learning, and making this is a good way to hack my way into it and get my hands dirty, I even coded in `elisp` for the first time!


<a id="org8113f62"></a>

### First step change the indentation

I am not a big fan of the way `org-indent` handles things: I would like to use org-mode as a document editor, and having indentation is nice to find your way within the document, but it also eats up too much space and make the overall document unbalance. I could just turn off the indentation by not running `org-indent-mode`, but I have a better idea. Unfortunately, that involves messing a little with the code of `org-indent`. Since it is a part of `org-mode`, the code base is super large, and I don't even know where it is hosted. So I just decided to copy `org-indent.el` file locally and load it from this path. There two things that I modified in org-indent:

-   I remove the indentation for the headlines.
-   I forced the indentation of the text to be always the same

    Perfect, now this is what it looks like

![img](I_am_writing_a_nice_document_in_org-mode/2021-08-22_15-29-35_screenshot.png)


<a id="org3dea552"></a>

### Configure org-superstar mode

Now that we have done this, I want to use the extra space that I have on the left to display some information nicely, in place of those annoying stars at the beginning of every heading. So we'll manage them using the `org-superstar` package. It is a nice package that allows you to configure how to display these stars, and it can handle the last star, which is displayed as a prettified bullet, and all the other stars, that can either be hidden, or prettified by a "leader" symbol. I want to get rid of the fact that there are multiple stars, and only keep the last star of each line, so I set the leader symbol to be the empty string, so that they completely disappear, and stop taking any space. Now we have a problem: things are not aligned since as the depth of the subheading increase, the size of the star shrinks. We can fix that by setting the face for the prettified bullet. We will chose a fixed-pitch face with a defined height, so that we are certain that all the heading are aligned again. Here is what this looks like now:

![img](I_am_writing_a_nice_document_in_org-mode/2021-08-22_15-48-11_screenshot.png)

Not bad, but we can do better: Let's remove the bullets altogether now. To do this I will just replace the prettify symbols for the bullets by a blank space. However, I want to be sure that the alignment is kept straight, so let's use the unicode character U+2001, which is an em-quad space, that is a space of the same size as a character of the font. Now `org-superstar` has a really cool feature that allow us to display a special bullet for each of the todo-keywords. So we will use this to display which bullet, if any should be displayed instead of this space. This is not very difficult to set-up, just associate each to-do keyword with a symbol of your choice. Here is the code for that in my configuration. Don't worry if my choice of symbols seem a bit odd to you, it will all make sense soon.

    (use-package org-superstar
        :after org
        :hook (org-mode . org-superstar-mode)
        :config
          (set-face-attribute 'org-superstar-header-bullet nil :inherit 'fixed-pitched :height 180)
        :custom
        ;; set the leading bullet to be a space. For alignment purposes I use an em-quad space (U+2001)
        (org-superstar-headline-bullets-list '(" "))
        (org-superstar-todo-bullet-alist '(("DONE" . ?✔)
                                           ("TODO" . ?⌖)
                                           ("ISSUE" . ?)
                                           ("BRANCH" . ?)
                                           ("FORK" . ?)
                                           ("MR" . ?)
                                           ("MERGED" . ?)
                                           ("GITHUB" . ?A)
                                           ("WRITING" . ?✍)
                                           ("WRITE" . ?✍)
                                           ))
        (org-superstar-special-todo-items t)
        (org-superstar-leading-bullet "")
        )


<a id="org7deb661"></a>

### Hide the todo keyword

Since we are using the todo keywords to control which icon to set in the margin, it would be a bit annoying to have the keyword always written again next to the margin, so instead we will remove it by prettifying it as an empty space

    (defun tb/org-mode-setup ()
        ;; (org-indent-mode)
        (visual-line-mode 1)
        (setq prettify-symbols-unprettify-at-point 'right-edge)
        (push '("[ ]" .  "☐") prettify-symbols-alist)
        (push '("[X]" . "☑") prettify-symbols-alist)
        (push '("[-]" . "❍") prettify-symbols-alist)
        (push '("TODO" . "") prettify-symbols-alist)
        (push '("DONE" . "") prettify-symbols-alist)
        (push '("BRANCH" . "") prettify-symbols-alist)
        (push '("MR" . "") prettify-symbols-alist)
        (push '("MERGED" . "") prettify-symbols-alist)
        (push '("FORK" . "") prettify-symbols-alist)
        (push '("ISSUE" . "") prettify-symbols-alist)
        (push '("GITHUB" . "") prettify-symbols-alist)
        (push '("WRITING" . "") prettify-symbols-alist)
        (push '("WRITE" . "") prettify-symbols-alist)
        (prettify-symbols-mode))


<a id="org1591cde"></a>

### Add a face for the leading bullet

There is one last thing I wanted to add: I would like to customize the face of the special to-do bullet, to add colors and make things a bit more neat. Unfortunately, that's a feature that does not exist in `org-superstar`. Thankfully, `org-superstar` is just a single elisp file stored in a repo on github, so if it is not there, let's add it. So I forked the project and started working on it. Since I just wanted to add a small feature, it wasn't that big a deal, and now if you use my fork you can set a custom face for the bullet of each of the todo items. I made a pull-request on the original repo, so hopefully this feature will be in the main repo soon enough!

Where is what my config looks like with the modified repo:

    (defvar tb/org-todo-bullet-faces
        '(("TODO" . (:inherit base-todo-keyword-face :foreground "#FF8580"))
          ("ISSUE" . (:inherit base-todo-keyword-face :foreground "#FF8580"
                                :family "github-octicons" :height 160))
          ("BRANCH" . (:inherit base-todo-keyword-face :foreground "#D58422"
                                :family "github-octicons"))
          ("FORK" . (:inherit base-todo-keyword-face :foreground "#D58422"
                                :family "github-octicons"))
          ("MR" . (:inherit base-todo-keyword-face :foreground "#C7A941"
                            :family "github-octicons"))
          ("MERGED" . (:inherit base-todo-keyword-face :foreground "#75AD18"
                                :family "github-octicons"))
          ("GITHUB" . (:inherit base-todo-keyword-face :foreground "#BBBBBB"
                                :family "github-octicons" :height 160))
          ("DONE" . (:inherit base-todo-keyword-face :foreground "#75AD18"))
          ("IDEA" . (:inherit base-todo-keyword-face :foreground "#85AAFF"))
          ("WRITE" . (:inherit base-todo-keyword-face :foreground "#FF8580"))
          ("WRITING" . (:inherit base-todo-keyword-face :foreground "#C7A941"))
              ))

      (use-package org-superstar
      :straight '(org-superstar
                  :fork (:host github
                          :repo "thibautbenjamin/org-superstar-mode"))
      :after org
      :hook (org-mode . org-superstar-mode)
      :config
        (set-face-attribute 'org-superstar-header-bullet nil :inherit 'fixed-pitched :height 180)
      :custom
      ;; set the leading bullet to be a space. For alignment purposes I use an em-quad space (U+2001)
      (org-superstar-headline-bullets-list '(" "))
      (org-superstar-todo-bullet-alist '(("DONE" . ?✔)
                                         ("TODO" . ?⌖)
                                         ("ISSUE" . ?)
                                         ("BRANCH" . ?)
                                         ("FORK" . ?)
                                         ("MR" . ?)
                                         ("MERGED" . ?)
                                         ("GITHUB" . ?A)
                                         ("WRITING" . ?✍)
                                         ("WRITE" . ?✍)
                                         ))
      (org-superstar-special-todo-items t)
      (org-superstar-leading-bullet "")
      (org-superstar-todo-bullet-face-alist tb/org-todo-bullet-faces)
      )

So now maybe you understand my weird choice of bullet for some of the items: they are displayed with the font `github-octicons`, in which they correspond to actual symbols of github!


<a id="orga044bc1"></a>

# Putting everything together

![img](/home/thibaut/Pictures/inconified1.png)
![img](/home/thibaut/Pictures/iconifed2.png)


<a id="orgfbf177e"></a>

# What is left to do?

Well obviously many things in the current document are not right. Let's create a block with a to-do listing everything that should be done.


<a id="org39f4f9f"></a>

## TODO Make org-mode prettier <code>[2/5]</code>

-   [X] Fix indentation
-   <del>[ ] Tabs at paragraph start</del>
-   [ ] Fix spacing
-   [ ] Grey line under level 1 headers
-   [ ] Center some elements ?
-   [X] Use custom faces for the org-superstar bullets

For one the indentation is not really correct. I would like every paragraph to start with a tab, but everything else to be aligned on the left. In particular, I do not like that the indentation depends on which subheading we are in. This is nice when editing code, but org is a text format! I also don't like that the spacing is always the same no matter which subheading we are under. It makes the document looks quite bulky when it could have been much better with proper spacing. I would also like to have a thin grey line under the level one subheading, like there is in a few markdown interpreters, such as in github I think. This might sound weird at first, but I like the feel it gives to the documents. Finally, I really would love to be able to center some elements. It might not sound like much but it makes a world of difference. Let's make a list of all these.


<a id="org200230c"></a>

### MR Set custom faces for the todo-bullets in org-superstar mode <code>[3/3]</code>

I have created my own branch for doing just that. I am pretty happy with the result. I will try it out for inline todo as well, and clean it up. After that, I will create a merge request and see if I can contribute to org-superstar mode !

-   [X] Test the changes for inline to-do's
-   [X] Update the documentation
-   [X] Submit a PR


<a id="orgae6d75b"></a>

## Additional ideas

I think it would be very nice to have a way to hide or prettify additional information, and reveal them at will. For instance, I would like to prettify the file header in order to display a line and just the title of the document (but without the `#+title:` in front of it). I am a big fan of things getting prettified/unprettified while the cursor hits them, and it would be super cool to make this happen in this case as well. I also believe there is a `org-sticky-header` (or something like that) package that would let me display this at all time. I also believe it would be cool to prettify the logbook and also the scheduled/deadline/timestamps attached to every item by making them disappear. I know it might sound scary, but I don't believe I really want to check what I have to do by looking at a text file, and I would rather have them pop up in the agenda instead.
