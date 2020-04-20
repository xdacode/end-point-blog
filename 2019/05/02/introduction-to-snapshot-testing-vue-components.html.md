---
author: "Patrick Lewis"
title: "Introduction to Snapshot Testing Vue Components"
tags: vue, testing
gh_issue_number: 1520
---

<img src="/blog/2019/05/02/introduction-to-snapshot-testing-vue-components/banner.jpg" alt="Camera and instant photos" /> [Photo](https://www.flickr.com/photos/freestocks/34484018071) by [freestocks](https://www.flickr.com/photos/freestocks), used under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/)

[Snapshot Testing](https://jestjs.io/docs/en/snapshot-testing) is one of the features of the [Jest](https://jestjs.io/en/) testing framework that most interested me when I began researching methods for testing Vue.js applications. Most of my testing experience has involved writing many verbose RSpec unit tests for Rails applications, and the promise of being able to use snapshot tests to cover more of a Vue component’s output while writing less code appealed to me. Snapshot testing does have its [critics](https://engineering.ezcater.com/the-case-against-react-snapshot-testing), so I have been interested to start exploring snapshot tests myself to see if they can be a valuable addition to my testing toolkit, or if they are not worth the effort.

Snapshot testing gets its name from the mechanism used to determine whether tests pass or fail by comparing them to a previously-approved reference point, or “snapshot”. With Jest snapshot testing of Vue components, the snapshot takes the form of a text file with a ‘.snap’ extension stored within a `__snapshots__` subdirectory alongside the test files:

<img src="/blog/2019/05/02/introduction-to-snapshot-testing-vue-components/tree.png" alt="Directory structure" />

I decided to generate a new project using [Vue CLI](https://cli.vuejs.org/) to do my first experiments with snapshot testing in a sample project. The project generated by Vue CLI includes one ‘HelloWorld’ component with a Jest unit test file included, so it made a good starting point for converting over to snapshot testing.

The generated test file was:

```javascript
// HelloWorld.spec.js
import { shallowMount } from '@vue/test-utils'
import HelloWorld from '@/components/HelloWorld.vue'

describe('HelloWorld.vue', () => {
  it('renders props.msg when passed', () => {
    const msg = 'new message'
    const wrapper = shallowMount(HelloWorld, {
      propsData: { msg }
    })
    expect(wrapper.text()).toMatch(msg)
  })
})
```

and I converted it to use a snapshot test by changing one `expect` line:

```javascript
// HelloWorld.spec.js
import { shallowMount } from '@vue/test-utils'
import HelloWorld from '@/components/HelloWorld.vue'

describe('HelloWorld.vue', () => {
  it('renders props.msg when passed', () => {
    const msg = 'new message'
    const wrapper = shallowMount(HelloWorld, {
      propsData: { msg }
    })
    expect(wrapper).toMatchSnapshot()
  })
})
```

Converting this test to a snapshot test allows me to test the entire output of the component in one pass by comparing it to a stored snapshot of itself.

Running jest for the first time with the new test file will generate a “known good” reference snapshot:

<img src="/blog/2019/05/02/introduction-to-snapshot-testing-vue-components/jest1.png" alt="Jest output" />

And running the same command a second time will now check the component against the stored snapshot:

<img src="/blog/2019/05/02/introduction-to-snapshot-testing-vue-components/jest2.png" alt="Jest output" />

In a contrived effort to see what a failing test looks like, I updated the template of the HelloWorld component being tested and changed its text interpolation:<br/>
from `{{ msg }}` to `{{ msg.toUpperCase() }}`.

The snapshot test for this component now fails and indicates how the rendered output differs from the previously-stored snapshot, where the interpolated string is converted to uppercase:

<img src="/blog/2019/05/02/introduction-to-snapshot-testing-vue-components/jest3.png" alt="Jest output" />

If the change I made to the template was a desired one, I could now run `jest -u` to update the stored snapshot, acknowledging that the change was intended and making this version the new reference. The Jest documentation recommends [treating snapshots as code](https://jestjs.io/docs/en/snapshot-testing#1-treat-snapshots-as-code) and committing them as you would any other type of code or test, so it would make sense to commit the updated snapshot alongside the change to the component itself.

This is a very basic example, but hints at the power available from using snapshot testing to provide a large amount of test coverage without much code by comparing the current output of a component against a previously-approved reference version of itself. I’m looking forward to putting this style of testing into practice in some of my Vue projects and seeing how useful snapshot tests turn out to be in a real-world workflow.