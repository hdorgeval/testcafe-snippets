# Code snippets for TestCafe

[This Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=hdorgeval.testcafe-snippets) provides code snippets for TestCafe.

Use the `tc-` prefix to access snippets:

- [tc-angularjs-enable-debug](#tc-angularjs-enable-debug)
- [tc-angularjs-get-object-from-scope](#tc-angularjs-get-object-from-scope)
- [tc-client-function-ajax-request-with-jquery](#tc-client-function-ajax-request-with-jquery)
- [tc-client-function-get-browser-user-agent](#tc-client-function-get-browser-user-agent)
- [tc-client-function-get-something-from-dom](#tc-client-function-get-something-from-dom)
- tc-client-function-get-window-state
- tc-client-function-measure-execution-time
- tc-client-function-read-localstorage
- tc-client-function-scroll-to-element
- tc-client-function-set-something-in-dom
- tc-client-function-write-to-localstorage
- tc-copy-paste-text
- tc-filter-hidden-elements
- tc-filter-visible-elements
- tc-fixture-with-start-page
- tc-fixture-with-start-page-and-hooks
- tc-get-selected-option
- tc-import
- tc-iterate-over-selected-checkboxes
- tc-maximize-window
- tc-navigate-to-url
- tc-pause-test-for-client-side-debugging
- tc-reload-browser
- tc-remove-text
- tc-selector-with-options
- tc-select-an-option-by-content
- tc-select-an-option-by-content-with-regex
- tc-select-an-option-by-exact-content
- tc-select-a-radio-button-by-value
- tc-select-first-option
- tc-select-last-option
- tc-set-window-size-to-custom-width-and-height
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
