## For Nette Framework

Flexible utility script for AJAX. Supports snippets, redirects etc.

#### License

MIT

#### Dependencies

jQuery 1.7

## Installation

1. Get the source code from Github.
2. Copy `nette.ajax.js` to your directory with Javascript files.
3. Link the file in your templates (usually in `app/@layout.latte`, after jQuery!).
4. Put somewhere the initialization routine:

```js
$(function () {
	$.nette.init();
});
```

## Usage

By defaults all links and forms with CSS class `ajax` will be instantly ajaxified. Behavior can be altered in configuration of `init` extension. Object returned by call `var init = $.nette.ext('init');` has these props:

<table>
	<tr>
		<th>name</th>
		<th>default value</th>
		<th>description</th>
	</tr>
	<tr>
		<td><code>linkSelector</code></td>
		<td><code>a.ajax</code></td>
		<td>CSS selector for links</td>
	</tr>
	<tr>
		<td><code>formSelector</code></td>
		<td><code>form.ajax</code></td>
		<td>CSS selector for forms</td>
	</tr>
	<tr>
		<td><code>buttonSelector</code></td>
		<td><code>input.ajax[type="submit"], input.ajax[type="image"]</code></td>
		<td>CSS selector for form elements responsible for submit</td>
	</tr>
</table>

Ajaxification is bound to `click` (`submit`) event in `nette` namespace. Ajaxification of specific link can be canceled with code like this (while other callbacks will remain):

```js
$('a.no-ajax').off('click.nette');
```

If this implementation doesn't suffice you, it can be overriden with custom solution, e.g.:

```js
$.nette.init(function (ajaxHandler) {
	$('a.ajax:not(.no-ajax)').live('click', ajaxHandler);
});
```

## Extensions

Ajaxification envelopes standard `$.ajax()` call and extends it with several events, that can be hooked with custom callbacks. Set of associated callbacks is called **extension**. Snippets processing, ability to cancel running request by *Escape*... all this functionality is implemented in form of extensions. Registration of extension looks like this:

```js
$.nette.ext('name', {
    event1: function () {
    },
    event2: ...
}, {
    // ... shared context (this) of all callbacks
});
```

First argument is name. It's optional.

Second argument should be hash of callbacks with keys corresponding to names of events. These events are available:

<table>
	<tr>
		<th>name</th>
		<th>arguments</th>
		<th>description</th>
	</tr>
	<tr>
		<td><code>init</code></td>
		<td></td>
		<td>Called just once during <code>$.nette.init();</code> call.</td>
	</tr>
	<tr>
		<td><code>load</code></td>
		<td><code>requestHandler</code></td>
		<td>Should contain ajaxification. <code>requestHandler</code> can be bound to UI events to initiate AJAX request.</td>
	</tr>
	<tr>
		<td><code>before</code></td>
		<td><code>jqXHR</code>, <code>settings</code></td>
		<td>Called immediatelly before AJAX request execution. If FALSE is returned, request won't start.</td>
	</tr>
	<tr>
		<td><code>start</code></td>
		<td><code>jqXHR</code>, <code>settings</code></td>
		<td>Called immediatelly after start of AJAX request.</td>
	</tr>
	<tr>
		<td><code>success</code></td>
		<td><code>payload</code></td>
		<td>Called after successful completion of AJAX request. Equivalent to <code>$.ajax( ... ).done( ...</code>.</td>
	</tr>
	<tr>
		<td><code>complete</code></td>
		<td></td>
		<td>Called after every AJAX request completion. Equivalent to <code>$.ajax( ... ).always( ...</code>.</td>
	</tr>
	<tr>
		<td><code>error</code></td>
		<td><code>jqXHR</code>, <code>status</code>, <code>error</code></td>
		<td>Called in case of failure of AJAX request. Equivalent to <code>$.ajax( ... ).fail( ...</code>.</td>
	</tr>
</table>

Extension can be disabled:

```js
$.nette.ext('name', null);
```

Extension can be configured. Its context can be acquired by:

```js
var context = $.nette.ext('name');
```

## Default extensions

<table>
	<tr>
		<th>name</th>
		<th>description</th>
	</tr>
	<tr>
		<td><code>validation</code></td>
		<td>Limits the ajaxification to clicks/submits without <em>Ctrl</em>, <em>Alt</em> or <em>Shift</em> key, local links and valid form submits.</td>
	</tr>
	<tr>
		<td><code>forms</code></td>
		<td>Adds support for submitting forms with all data, name of clicked button and coordinates of click in <code>[type=image]</code> inputs.</td>
	</tr>
	<tr>
		<td><code>snippets</code></td>
		<td>Updates DOM by <code>snippets</code> array in response (default Nette handling of Latte snippets).</td>
	</tr>
	<tr>
		<td><code>redirect</code></td>
		<td>Executes redirect to different URL (when <code>$this->redirect()</code> is called in presener). Can be replaced by <code>history</code> extension.</td>
	</tr>
	<tr>
		<td><code>unique</code></td>
		<td>Keeps only request running at the same moment.</td>
	</tr>
	<tr>
		<td><code>abort</code></td>
		<td>Allows user to abort running request by pressing <em>Escape</em>.</td>
	</tr>
	<tr>
		<td><code>init</code></td>
		<td>Default ajaxification.</td>
	</tr>
</table>
