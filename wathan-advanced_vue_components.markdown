Advanced Vue.js Component Design
--------------------------------

_Adam Wathan, February 7, 2018, 10am EST_

### Introduction

OMG Scoped Slots Can Do That?

[Demo of forum thread 'tag' component]

"It would be cool if I could turn this component into a library..." ...if you
want to do that, then it needs to be flexible / customizable.

#### Customization Option 1: Style Props

Real-World example: **Stripe**

This can be a bit of a pain because of all the inputs to pass in.

#### Customization Option 2: CSS Hooks

This forces people to write CSS that adopts your own naming convention, which
is less than desirable; you also might run into naming conflicts.

- - -

But there is always more customization that people want, and it's hard to
provide for all the cases in CSS alone.  "Customization is a bottomless pit."

#### Customization Option 3: Give Up
 
 Seriously.

### Scoped Slots

To introduce this subject, let's first talk about _boring slots_.  These allow
you to provide placeholders for the end user, but _they are really just props_
under the hood.

How can we apply the slots method to the 'tag' component we saw earlier?

With 'boring slots', you run into a scoping problem (the component sub-template
doesn't have access to external properties).

To get around this, you make the sub-template a callback (like a React render
prop).  In Vue, to accomplish this you use a _scoped slot_.

[Live code demonstrating the transformation from boring slots to scoped slots.]

```
<slot name="tags" v-for="tag in tags" :tag="tag"></slot>
...
<span slot="tag" slot-scope="props" class="tags-input-tag">
```

### Scoped Slot Prop Categories

1. State / Data (simple values)
2. Actions (removeTag(tag) is an example)
3. Event Handlers / Event Handler Groups
4. Bindings / Groups of Bindings

```
<slot name="tags" v-for="tag in tags" :tag="tag" :remove-tag="removeTag"></slot>
...
// Slot scope syntax:
slot-scope="{ newTag, onInput, handleTagBackspace, addTag }"

// Does this pattern force the end user to know too much?
// Enter 'Event Handler Groups'

// Define all the handlers in one object and bind them in:
v-on="{ input: onInput, keydown: onKeydown }"

...

:event-handlers="{
    input: (e) => { newTag = e.target.value },
    keydown: (e) => {
        if (e.keyCode === 8) {
            this.handleTagBackspace()
        }
        if (e.keyCode === 13) {
            e.preventDefault()
            this.addTag()
        }
    }"
:bindings="{
    value: newTag
}"

slot-scope="{..., event-handlers, bindings }"
    v-on="eventHandlers"
    v-bind="bindings"
/>
```

### Supporting Alternate Layouts

Single-slot abstraction.

```
<template>
    <div class="tags-input">
        <slot
            :tags="tags"
            :remove-tag="removeTag"
            :input-bindings="{
                
            }"
            :input-event-handlers="{
                input: (e) => { newTag = e.target.value },
                keydown: (e) => {
                    if (e.keyCode === 8) {
                        this.handleTagBackspace()
                    }
                    if (e.keyCode === 13) {
                        e.preventDefault()
                        this.addTag()
                    }
                }
            }"
        /></slot>
    </div>
</template>
```

You can also get around the single '<div>' node wrapper by writing a render
function.

```
render() {
    return this.$scopedSlots.default({
        tags: this.tags,
        removeTag: this.removeTag,
        inputEventHandlers: {
            //
        },
        inputBindings: {
            //
        }
    })
},
```

You can fix re-implementing the styling with every component instance by writing
_wrapper components_.

[Rapid live coding, pulling the tags input template into a wrapper]

### Wrap-Up

Scoped slots allow, in the way demonstrated, for building of customizable and
flexible Vue components for public libraries / distribution.
