# Code snippets for TestCafe ([say goodbye to flackyness](flacky-or-not-flacky.md))

[This Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=hdorgeval.testcafe-snippets) provides code snippets for TestCafe.

Use the `tc-` prefix to access snippets:

- [tc-angularjs-enable-debug](#tc-angularjs-enable-debug)
- [tc-angularjs-get-object-from-scope](#tc-angularjs-get-object-from-scope)
- [tc-client-function-ajax-request-with-jquery](#tc-client-function-ajax-request-with-jquery)
- [tc-client-function-get-browser-user-agent](#tc-client-function-get-browser-user-agent)
- [tc-client-function-get-something-from-dom](#tc-client-function-get-something-from-dom)
- [tc-client-function-get-window-state](#tc-client-function-get-window-state)
- [tc-client-function-measure-execution-time](#tc-client-function-measure-execution-time)
- [tc-client-function-read-localstorage](#tc-client-function-read-localstorage)
- [tc-client-function-scroll-to-element](#tc-client-function-scroll-to-element)
- [tc-client-function-set-something-in-dom](#tc-client-function-set-something-in-dom)
- [tc-client-function-write-to-localstorage](#tc-client-function-write-to-localstorage)
- [tc-copy-paste-text](#tc-copy-paste-text)
- [tc-filter-hidden-elements](#tc-filter-hidden-elements)
- [tc-filter-visible-elements](#tc-filter-visible-elements)
- [tc-fixture-with-start-page](#tc-fixture-with-start-page)
- [tc-fixture-with-start-page-and-hooks](#tc-fixture-with-start-page-and-hooks)
- [tc-get-selected-option](#tc-get-selected-option)
- [tc-import](#tc-import)
- [tc-iterate-over-selected-checkboxes](#tc-iterate-over-selected-checkboxes)
- [tc-maximize-window](#tc-maximize-window)
- [tc-navigate-to-url](#tc-navigate-to-url)
- [tc-pause-test-for-client-side-debugging](#tc-pause-test-for-client-side-debugging)
- [tc-reload-browser](#tc-reload-browser)
- [tc-remove-text](#tc-remove-text)
- [tc-selector-with-options](#tc-selector-with-options)
- [tc-select-an-option-by-content](#tc-select-an-option-by-content)
- [tc-select-an-option-by-content-with-regex](#tc-select-an-option-by-content-with-regex)
- [tc-select-an-option-by-exact-content](#tc-select-an-option-by-exact-content)
- [tc-select-a-radio-button-by-value](#tc-select-a-radio-button-by-value)
- [tc-select-first-non-empty-option](#tc-select-first-non-empty-option)
- [tc-select-first-option](#tc-select-first-option)
- [tc-select-last-option](#tc-select-last-option)
- [tc-set-window-size-to-custom-width-and-height](#tc-set-window-size-to-custom-width-and-height)
- tc-set-window-size-to-fit-device
- tc-take-screenshot-of-an-element-in-dom
- tc-test
- tc-test-with-hooks
- tc-type-text
- tc-wait-for-a-selector-to-appear
- tc-wait-for-a-selector-to-disappear

## Supported languages (file extensions)

* JavaScript (.js)
* TypeScript (.ts)

## Details

### tc-angularjs-enable-debug

```typescript
// enable Angular debug info
const reloadWithDebugInfo = ClientFunction(() => {
    const angular = (window as any).angular;
    if (angular) {
        angular.reloadWithDebugInfo();
    }
});
await reloadWithDebugInfo();
```

### tc-angularjs-get-object-from-scope

```typescript
// get object from angularjs scope
const getCustomObject = ClientFunction( () => {
    const $ = (window as any).jQuery;
    const scope = $("selector").scope();
    // inspect, in dev tools, the scope object todiscover available objects
    // tslint:disable-next-line:no-console
    console.log("scope: ", scope);
    return scope.xxx;
});
const myObject = await getCustomObject();
```

### tc-client-function-ajax-request-with-jquery

```typescript
// Client-side ajax request
// client-side function used for sending the ajax request
const send = ClientFunction( (req: any) => {
    return new Promise( (resolve) => {
        const json: string = JSON.stringify(req);
        const $ = (window as any).jQuery;
        $.ajax({
            contentType: "application/json; charset=utf-8",
            data: json,
            dataType: "json",
            type: "POST",
            url: "https://reqres.in/api/users",
        })
        .always( (httpResponse: XMLHttpRequest) => {
            if (httpResponse && httpResponse.getAllResponseHeaders ===undefined) {
                resolve(httpResponse);
                return;
            }
            resolve({
                responseHeaders: httpResponse.getAllResponseHeaders(),
                responseText: httpResponse.responseText,
                status: httpResponse.status,
                statusText: httpResponse.statusText,
            });
        });
    });
});
// request object that will be posted to the backend server
const request = {
    firstName: "john",
    lastName: "Doe",
    requestTime: Date(),
};
// send request and get response
const response: XMLHttpRequest | any = await send(request);
```

### tc-client-function-get-browser-user-agent

```typescript
// you need to import {ClientFunction} from "testcafe";
const getBrowserUserAgent = ClientFunction(() => navigator.userAgent.toString());
const userAgent = await getBrowserUserAgent();
```

### tc-client-function-get-something-from-dom

```typescript
// you need to import {ClientFunction} from "testcafe";
// this sample will get the details of a selected input
// see http://devexpress.github.io/testcafe/example/
// <label for="windows">
//     <input type="radio" name="os" id="windows" value="Windows">
//     Windows
// </label>
const inputSelector = Selector('label[for="windows"] input');
const getInputDetails = ClientFunction((selector: Selector) => {
    return new Promise( (resolve) => {
        const element: any = selector();
        // check, in developper tools, what are the available properties in element
        // tslint:disable-next-line:no-console
        console.log("element:", element);
        resolve({
            checked: element.checked,
            disabled: element.disabled,
            id: element.id,
            name: element.name,
            type: element.type,
            value: element.value,
        });
    });
});
const inputDetails = await getInputDetails(inputSelector);
```

### tc-client-function-get-window-state

```typescript
// you need to import {ClientFunction} from "testcafe";
const getWindowState = ClientFunction(() => ({
    height:      window.innerHeight,
    isMaximized: window.outerWidth >= window.screen.availWidth && window.outerHeight >=window.screen.availHeight,
    width:       window.innerWidth,
}));
const windowState = await getWindowState();
```

### tc-client-function-measure-execution-time

```typescript
// you need to import {ClientFunction} from "testcafe";
const stopWatch = {
    elapsedTimeInMilliseconds: ClientFunction(() => Date.now() - (window as any).startTime),
    start: ClientFunction(() => (window as any).startTime = Date.now()),
};
await stopWatch.start();
// place here the code to instrument,
const elapsed = await stopWatch.elapsedTimeInMilliseconds();
await t.expect(elapsed).lt(1000);
```

### tc-client-function-read-localstorage

```typescript
// you need to import {ClientFunction} from "testcafe";
const getLocalStorageValueByKey = ClientFunction((key: string) => {
    return new Promise( (resolve) => {
        const result = localStorage.getItem(key);
        resolve(result);
    });
});
const value = await getLocalStorageValueByKey("mykey");
```

### tc-client-function-write-to-localstorage

```typescript
// you need to import {ClientFunction} from "testcafe";
const setLocalStorage = ClientFunction((key: string, value: string) => {
    return new Promise( (resolve, reject) => {
        try {
            localStorage.setItem(key, value);
            resolve();
        } catch (error) {
            reject(error);
        }
    });
});
await setLocalStorage("mykey", "myValue");
```

### tc-client-function-scroll-to-element

```typescript
// you need to import {ClientFunction} from "testcafe";
// this sample will scroll to a label
// see http://devexpress.github.io/testcafe/example/
// <label for="windows">
//     <input type="radio" name="os" id="windows" value="Windows">
//     Windows
// </label>
const inputSelector = Selector('label[for="windows"]');
const scrollToElement = ClientFunction((selector: Selector, offset: {x: number, y: number}) => {
    return new Promise( (resolve) => {
        const element: any = selector();
        if (element && element.scrollIntoView) {
            element.scrollIntoView();
        }
        // do a small shift up and left
        if (window && window.scrollBy && offset) {
            window.scrollBy(offset.x, offset.y);
        }
        resolve();
    });
});
await scrollToElement(inputSelector, {x: 20, y: -20});
```

### tc-client-function-set-something-in-dom

```typescript
// you need to import {ClientFunction} from "testcafe";
// this sample will replace a checkbox label
// see http://devexpress.github.io/testcafe/example/
// <label for="windows">
//     <input type="radio" name="os" id="windows" value="Windows">
//     Windows
// </label>
const mySelector = Selector('label[for="windows"]');
const setLabel = ClientFunction((selector: Selector, value: string) => {
    const element: any = selector();
    // check, in developper tools, what are the available properties in element
    // tslint:disable-next-line:no-console
    console.log("element:", element);
    element.firstElementChild.nextSibling.replaceWith(value);
});
await setLabel(mySelector, "Windows 10");
```

### tc-copy-paste-text

```typescript
// see http://devexpress.github.io/testcafe/example/
const input = Selector('input#developer-name[type="text"]');
await t // copy paste a text in an input box and press tab
    .setTestSpeed(0.7)
    .hover(input)
    .expect(input.hasAttribute("disabled")).notOk({timeout: 5000})
    .click(input)
    .typeText(input, "john doe", {replace: true, paste: true})
    .pressKey("tab");
```

### tc-filter-hidden-elements

```typescript
// get all hidden inputs
// see http://devexpress.github.io/testcafe/example/
const inputs = Selector("input");
const hiddenInputs =  inputs.filterHidden(); // v0.19.0
await t.expect(hiddenInputs.count).eql(1);
console.log(`first hiddenInput is '${await hiddenInputs.nth(0).id}'`);
```

### tc-filter-visible-elements

```typescript
// get all visible inputs
// see http://devexpress.github.io/testcafe/example/
const inputs = Selector("input");
const visibleInputs = inputs.filterVisible(); // v0.19.0
await t.expect(visibleInputs.count).eql(11);
console.log(`first visibleInputs is '${await visibleInputs.nth(0).id}'`);
```

### tc-fixture-with-start-page

```typescript
fixture("My Fixture")
    .page("http://myurl");

```

### tc-fixture-with-start-page-and-hooks

```typescript
fixture("My Fixture")
    .page("http://myurl")
    .before(async (ctx) => {
        // inject in the fixture context any object you want to share beetween tests
        ctx.config = config;
    })
    .beforeEach(async (t) => {
        // inject in the test context any input data needed to run the test
        t.ctx.inputData = inputData;
        // put here all actions that are common to all tests
});
```

### tc-get-selected-option

```typescript
// get the selected option
// see http://devexpress.github.io/testcafe/example/
const select = Selector("select#preferred-interface");
const selectedOption = select
        .find("option")
            .filter((node) => {
                const option = node as HTMLOptionElement;
                if (option && option.selected) {
                    return true;
                }
                return false;
            })
            .nth(0);
const selectedOptionContent = await  selectedOption.textContent;
```

### tc-import

```typescript
import "testcafe";
import {ClientFunction, Selector} from "testcafe";
```

### tc-iterate-over-selected-checkboxes

```typescript
// iterate over selected checkboxes
// see http://devexpress.github.io/testcafe/example/
const selectedCheckboxes = Selector('input[type="checkbox"]')
    // select all checboxes whose name value is in a set of predefined values
    .withAttribute("name", /custom-name|remote|re-using|background|CI|analysis/)
        .filter((node) => {
            const checkbox = node as HTMLInputElement;
            if (checkbox && checkbox.checked) {
                return true;
            }
            return false;
        });
const checkedCount = await selectedCheckboxes.count;
const checkedIds = [];
for (let i = 0; i < checkedCount; i++) {
    checkedIds.push(await selectedCheckboxes.nth(i).id);
}
// now you can make your business check from the checkedIds array
```

### tc-maximize-window

```js
await t // set the window size to it's max size
    .maximizeWindow();
```

### tc-navigate-to-url

```js
await t // navigate to url
    .navigateTo("url");
```

### tc-pause-test-for-client-side-debugging

```js
await t.debug();
```

### tc-reload-browser

```js
// you need to import {ClientFunction} from "testcafe";
const reloadBrowser = ClientFunction(() => window.location.reload(true));
await reloadBrowser();
```

### tc-remove-text

```js
// see http://devexpress.github.io/testcafe/example/
const input = Selector('input#developer-name[type="text"]');
await t // remove the text present in an input box and press tab
    .setTestSpeed(0.7)
    .hover(input)
    .expect(input.hasAttribute("disabled")).notOk({timeout: 5000})
    .click(input)
    .pressKey("ctrl+a delete")
    .pressKey("tab");
```

### tc-selector-with-options

```js
// see http://devexpress.github.io/testcafe/example/
const mySelector = Selector('input#developer-name')
    .with({timeout: 5000, visibilityCheck: true})
    .nth(0);
```

### tc-select-an-option-by-content

```js
// see http://devexpress.github.io/testcafe/example/
const select = Selector("select#preferred-interface");
await t // select an option by its content and press tab
    .setTestSpeed(0.7)
    .hover(select)
    .expect(select.hasAttribute("disabled")).notOk({timeout: 5000})
    .click(select)
    .click(select
        .find("option")
        .withText("Commmand Line")
        .nth(0))
    .pressKey("tab");
```

### tc-select-an-option-by-content-with-regex

```js
// see http://devexpress.github.io/testcafe/example/
const select = Selector("select#preferred-interface");
await t // select an option by content with a Regex and press tab
    .setTestSpeed(0.7)
    .hover(select)
    .expect(select.hasAttribute("disabled")).notOk({timeout: 5000})
    .click(select)
    .click(select
        .find("option")
        .withText(new RegExp(`^.*API$`))
        .nth(0))
    .pressKey("tab");
```

### tc-select-an-option-by-exact-content
```js
// see http://devexpress.github.io/testcafe/example/
    const select = Selector("select#preferred-interface");
    await t // select an option by its exact content and press tab
        .setTestSpeed(0.7)
        .hover(select)
        .expect(select.hasAttribute("disabled")).notOk({timeout: 5000})
        .click(select)
        .click(select
            .find("option")
            .withExactText("JavaScript API")
            .nth(0))
        .pressKey("tab");
```

### tc-select-a-radio-button-by-value

```typescript
// see http://devexpress.github.io/testcafe/example/
// select a radio button by it's input value
const radioButtonValue = "Linux"; // the value to search
const radioButton = Selector('input[type="radio"]')
    .withAttribute("name", "os") // select all radio buttons within the group named 'os'
        .filter((node) => {
            const button = node as HTMLInputElement;
            if (button && button.value === radioButtonValue) {
                return true;
            }
            return false;
        }, {radioButtonValue})
        .nth(0);
await t
    .hover(radioButton)
    .expect(radioButton.hasAttribute("disabled")).notOk({timeout: 5000})
    .click(radioButton)
    .pressKey("tab");
```

### tc-select-first-non-empty-option

```typescript
// select first non-empty option
const selector = Selector("select").nth(0);
const firstNonEmptyOption = selector
    .find("option")
        .filter((node) => {
            const option = node as HTMLOptionElement;
            if (option && option.innerText && option.innerText.trim() !== "") {
                return true;
            }
            return false;
        })
        .nth(0);
await t
    .hover(selector)
    .click(selector)
    .click(firstNonEmptyOption);
```

### tc-select-first-option

```typescript
// see http://devexpress.github.io/testcafe/example/
const select = Selector("select#preferred-interface");
await t // select the first option and press tab
    .setTestSpeed(0.7)
    .hover(select)
    .expect(select.hasAttribute("disabled")).notOk({timeout: 5000})
    .click(select)
    .click(select
        .find("option")
        .nth(0))
    .pressKey("tab");
```

### tc-select-last-option

```typescript
// see http://devexpress.github.io/testcafe/example/
const select = Selector("select#preferred-interface");
await t // select the last option and press tab
    .setTestSpeed(0.7)
    .hover(select)
    .expect(select.hasAttribute("disabled")).notOk({timeout: 5000})
    .click(select)
    .click(select
        .find("option")
        .nth(-1))
    .pressKey("tab");
```

### tc-set-window-size-to-custom-width-and-height

```js
await t // set the window size
    .resizeWindow(1024, 600);
```
