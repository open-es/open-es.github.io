---
layout: guide
lang: en
ref: forms
title: Forms
description: What the user sees, and how a form is bound to data.
section: studio
order: 7
permalink: /guide/forms/
---

A form is a window the user works in: a document being entered, a list of products, a report
with its settings. The same form runs in the desktop application and in the browser.

## Generated or drawn

An object with no form of its own gets a **generated** one: the platform builds it from the
object's attributes and tables. That is enough to start, and it keeps up with the metadata.

To control the layout, add a form under the object's **Forms**, then set it as the default in
the object's properties: default object form, list form, choice form.

## The form editor

Opening a form shows two tabs:

- **Designer**: the layout.
- **Code**: the form's module, its event handlers and procedures.

Beside the layout:

| Panel | What it holds |
|---|---|
| **Attributes** | The data the form works on. The **main** attribute is the object the form was opened for; add more for anything else the form keeps |
| **Commands** | Actions the form offers: buttons, menu items |
| **Tree elements** | The controls on the form, as a tree, in the order they are laid out |

**Drag an attribute onto the form** and a control bound to it appears: a text box for a
string, a check box for a boolean, a table for a tabular section. Changing the field changes
the data, and the reverse.

## Controls

Text box, check box, radio button, button, table, tool bar, notebook (tabs), frame, static text
and line, gauge, slider, HTML box, chart, and the sizers that lay them out (box, grid, wrap,
static box). Each control's properties include its events.

## Layout

Controls are placed by **sizers**, not by coordinates. A form therefore resizes, changes
language and font size, and moves to the browser without being redrawn.

## Code on a form

An event in a control's or the form's properties is bound to a procedure in the form's module.
Rename or delete the handler in the editor and the studio offers to rename or delete the
procedure with it.

Do not assume data exists when the form opens. The studio previews forms without running
their data reads, so an open handler has to tolerate an empty source.
