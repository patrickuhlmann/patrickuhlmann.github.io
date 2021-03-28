---
layout: post
title:  "Vue 2 Typescript: How to test components with jest"
date:   2021-03-28 15:00:33 +0100
categories: vue testing jest
---
Most examples use plain javascript or fail to demonstrate how to run tests including mocking, props, emitting and verifying the template.

This is the component under test (a contact form). It allows sending a message and either shows a success confirmation in a div or opens a modal dialog on error.
{% highlight typescript %}
<script lang="ts">
import { ContactService } from "@/services";
import { AxiosUtil } from "@/utils";
import Modal from "@/components/modal.vue";

import { Component, Ref, Vue } from "vue-property-decorator";

@Component({
  components: {
    Modal,
  },
})
export default class Contact extends Vue {
  @Ref("errorModal") errorModal!: Modal;
  @Prop({}) readonly successMessage!: string;
  @Emit("formSent")
  onFormSent(state: boolean) {
    return state;
  }

  private success = false;
  private message = "";
  service: ContactService = new ContactService(AxiosUtil.getCockpitInstance());

  async send() {
    try {
      await this.service.submitForm({
        form: {
          message: this.name,
        },
      });
      this.success = true;
      this.onFormSent(true);
    } catch (err) {
      this.errorModal.open();
      this.onFormSent(false);
    }
  }
}
</script>

<template>
  <section>
    <h1>Contact</h1>
    <textarea v-model="message"></textarea>
    <button :disabled="!message" v-on:click="send()">Send Message</button>

    <div id="success" v-if="success">
      <p>{{ successMessage }}</p>
    </div>

    <modal message="Failed to send message" ref="errorModal"></modal>
  </section>
</template>
{% endhighlight %}

And here a test:
{% highlight typescript %}
import { shallowMount, Wrapper } from "@vue/test-utils";
import Contact from "@/views/Contact.vue";

describe('Contact Page', () => {
  it("send fails", async () => {
    let wrapper: Wrapper<Kontakt & { [key: string]: any }>;

    const service = {
      submitForm: () => { throw new Error("SomeError"); }
    }

    const openErrorFunction = jest.fn();
    const errorModal = {
      open: () => openErrorFunction()
    }

    wrapper = await shallowMount(Kontakt, {
      stubs: [ "router-link" ],
      data: () => {
        return {
          service: service,
        };
      }, computed: {
        errorModal() {
         return errorModal;
        },
      }, propsData: {
      	successMessage: 'Message sent'
      }
    });
    await wrapper.vm.send();

    expect(openErrorFunction.mock.calls.length).toBe(1);
    const success = wrapper.find('#success');
    expect(success.exists()).toBe(false);
    
    const formSentEmitted = wrapper.emitted().formSent;
    expect(formSent![0]).toEqual([true]);
  })
});
{% endhighlight %}

The test instanciates the component using a mock service (that throws an error) and a mock modal dialog. It then calls the send method and verifies that the error dialog was opened and the success message is not displayed.

Noteworthy:
  * the @Ref are interpreted as computed properties
  * the ```& { [key: string]: any }``` prevents compilation errors as the wrapper is not typed
  * you can create a "mock" of a class simply by creating an ad-hoc object with the relevant methods of that class and a fake implementation (```jest.fn()``` or custom)
  * testing also works with async methods
  * the component including properties and methods can be accessed with ``ẁrapper.vm```. The IDE however will not be able to show (autocomplete, intellisense) you the methods or properties but it will work anyways.
  * ```jest.fn()``` has a property ```mock``` which allows to evaluate all method calls (how often, which arguments, ...)
  * stubs allows the definition of components that will be replaced with a stub
  * props can be filled while mounting
  * emitted events can be checked via wrapper
