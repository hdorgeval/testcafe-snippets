# Code snippets for TestCafe

[This Visual Studio Code Extension](https://marketplace.visualstudio.com/items?itemName=hdorgeval.testcafe-snippets) provides code snippets for TestCafe.

Use the `tc-` prefix to access snippets:

- [tc-angularjs-enable-debug](#tc-angularjs-enable-debug)
- [tc-angularjs-get-object-from-scope](#tc-angularjs-get-object-from-scope)
- tc-client-function-ajax-request-with-jquery
- tc-client-function-get-browser-user-agent
- tc-client-function-get-something-from-dom
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
