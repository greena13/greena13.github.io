---
layout: post
title: "Turbolinks Cheatsheet"
date: 2020-07-09 07:46:02 +0100
comments: true
categories: 
---

A cheatsheet for the often confusing behaviour of Turbolinks.

<!--more-->

## How it works

When you follow a link, Turbolinks automatically fetches the page:

* Swaps in its `<body>`
* Merges its `<head>`

...without incurring the cost of a full page load

Allows the server to respond with a full HTML page.

Works in all modern desktop and mobile browsers. Depends on the 

* HTML5 `History` API 
* `Window.requestAnimationFrame`

In unsupported browsers, Turbolinks gracefully degrades to standard navigation.

## Installation

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 412px;"/>
        <col style="width: 397px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 412px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">&lt;script&gt;</span> tag or a
                traditional concatenated JavaScript bundle
            </div>
        </td>
        <td style="width: 397px; padding: 8px; border: 1px solid;">
            <div>Automatically initializes itself when loaded</div>
        </td>
    </tr>
    <tr>
        <td style="width: 412px; padding: 8px; border: 1px solid;">
            <div>CommonJS or AMD module</div>
        </td>
        <td style="width: 397px; padding: 8px; border: 1px solid;">
            <div>Require the module, then call the provided <span
                    style="font-family: &quot;Courier New&quot;;">start()</span> function.
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 412px; padding: 8px; border: 1px solid;">
            <div><span style="font-size: 13px;">Ruby on Rails</span></div>
        </td>
        <td style="width: 397px; padding: 8px; border: 1px solid;">
            <div>Can use the <span style="font-family: &quot;Courier New&quot;;">turbolinks</span> RubyGem
                to install Turbolinks
            </div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div># Gemfile</div>
                <div>'turbolinks', '~&gt; 5.2.0’</div>
                <div><br/></div>
                <div>// application.js</div>
                <div>//= require turbolinks</div>
            </div>
        </td>
    </tr>
    </tbody>
</table>

## Troubleshooting

First identify if it’s a problem with:

**Page Load**:

* Initial page load (Uncommon - basically standard browser behaviour)

**Advance or Replace**:

* Displaying the preview of an application visit (flash of content or styling when navigating to pages the browser has already been to, or using the browser’s Forward button)
* After new page has been downloaded and applied

**Restoration**:

* Retrieving page from cache for restore visits (using the browsers Back button)

and then consult the relevant part of the table below.

## Cheatsheet

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 104px;"/>
        <col style="width: 86px;"/>
        <col style="width: 167px;"/>
        <col style="width: 246px;"/>
        <col style="width: 211px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td colspan="2" style="width: 190px; padding: 8px; border: 1px solid;">
            <div><b>Type of visit</b></div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div><b>Page Load</b></div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div><b>Advance or Replace</b></div>
        </td>
        <td style="width: 211px; padding: 8px; border: 1px solid;">
            <div><b>Restoration</b></div>
        </td>
    </tr>
    <tr>
        <td colspan="2" style="width: 190px; padding: 8px; border: 1px solid;">
            <div>Occurs when</div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div>First page is loaded or browser is refreshed</div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>Default: Clicking <span
                    style="font-family: &quot;Courier New&quot;;">&lt;a href&gt;</span> to the same
                domain, or calling <span style="font-family: &quot;Courier New&quot;;">Turbolinks.visit(location)</span>
            </div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><font face="Helvetica Neue"><b>Using replace instead of advance:</b></font></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;a href="/edit" <span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">data-turbolinks-action="replace"</span>&gt;Edit&lt;/a&gt;<br/>
                </div>
                <div><br/></div>
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">Turbolinks.visit</span>("/edit",
                    { action: "replace" })
                </div>
            </div>
            <div/>
        </td>
        <td style="width: 211px; padding: 8px; border: 1px solid;">
            <div>Browser’s <b>Back</b> or <b>Forward</b> buttons</div>
            <div><br/></div>
            <div>Internal operation: don’t  annotate links or invoke <span
                    style="font-family: &quot;Courier New&quot;;">Turbolinks.visit </span>with an
                action of <span style="font-family: &quot;Courier New&quot;;">restore</span>.
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="2" style="width: 190px; padding: 8px; border: 1px solid;">
            <div>Disabling</div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div><b>Limit Turbolinks to only a certain path across entire page</b></div>
            <div><br/></div>
            <div>Only load same-origin URLs that are prefixed with this path.</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;head&gt;</div>
                <div>  ...</div>
                <div>  <font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">&lt;meta
                    name="turbolinks-root" content="/app"&gt;</font></div>
                <div>&lt;/head&gt;</div>
            </div>
            <div><br/></div>
            <div><b>Disable for part of document</b></div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;a href="/" <font
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">data-turbolinks="false"</font>&gt;Disabled&lt;/a&gt;
                </div>
                <div><br/></div>
                <div>&lt;div <font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">data-turbolinks="false"</font>&gt;
                </div>
                <div>  &lt;a href="/"&gt;Disabled&lt;/a&gt;</div>
                <div>&lt;/div&gt;</div>
            </div>
            <div><br/></div>
            <div>Re-enable when ancestor has opted out:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;div data-turbolinks="false"&gt;</div>
                <div>  &lt;a href="/" <font
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">data-turbolinks="true"</font>&gt;Enabled&lt;/a&gt;
                </div>
                <div>&lt;/div&gt;</div>
            </div>
            <div/>
            <div><span style="font-family: Monaco;"><br/></span></div>
            <div><b>Cancel at runtime</b></div>
            <div><b><br/></b></div>
            <div>Listen to event <span style="font-family: &quot;Courier New&quot;;">turbolinks:before-visit</span>
                and use <span style="font-family: &quot;Courier New&quot;;">event.data.url</span> (or <span
                        style="font-family: &quot;Courier New&quot;;">$event.originalEvent.data.url</span>,
                when using jQuery) to decide whether to call <span
                        style="font-family: &quot;Courier New&quot;;">event.preventDefault().</span><br/>
            </div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><font face="Helvetica Neue">Disable showing a preview (if one is available) while
                page is being refetched (</font>still used for restoration visits):
            </div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;head&gt;</div>
                <div>  ...</div>
                <div> <font style="color: rgb(255, 38, 0); --inversion-type-color: simple;"> &lt;meta
                    name="turbolinks-cache-control" content=“no-preview"&gt;</font></div>
                <div>&lt;/head&gt;</div>
            </div>
            <div/>
        </td>
        <td style="width: 211px; padding: 8px; border: 1px solid;">
            <div><span style="font-weight: bold; font-size: 14px;">Disallowing caching</span></div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;head&gt;</div>
                <div>  ...</div>
                <div>  <font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">&lt;meta
                    name="turbolinks-cache-control" content="no-cache"&gt;</font></div>
                <div>&lt;/head&gt;</div>
            </div>
            <div><br/></div>
            <div>Force a full reload:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;head&gt;<br/></div>
                <div>  ...</div>
                <div>  <span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">&lt;meta name="turbolinks-visit-control" content="reload"&gt;</span>
                </div>
                <div>&lt;/head&gt;</div>
            </div>
            <div/>
            <div><span style="font-family: Monaco;"><br/></span></div>
            <div><span style="font-weight: bold;">Cancel at runtime</span></div>
            <div><span style="font-weight: bold;"><br/></span></div>
            <div><font face="Helvetica Neue">Does not fire </font><span
                    style="font-family: &quot;Courier New&quot;;">turbolinks:before-visit</span><font
                    face="Helvetica Neue">, so can’t be cancelled in this way</font></div>
            <div/>
        </td>
    </tr>
    <tr>
        <td colspan="2" style="width: 190px; padding: 8px; border: 1px solid;">
            <div>Navigation</div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>Prevents the browser from following the link</div>
            <div><br/></div>
            <div>Uses the <span style="font-family: &quot;Courier New&quot;;">History</span> API to
                push a new entry onto the browser’s history stack (changes current URL): <span
                        style="font-family: &quot;Courier New&quot;;"><font
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">history.pushState</font></span>.
            </div>
            <div><br/></div>
            <div>Request:</div>
            <div><br/></div>
            <div>Always requests the new page using <span
                    style="font-family: &quot;Courier New&quot;;">XMLHttpRequest</span></div>
        </td>
        <td style="width: 211px; padding: 8px; border: 1px solid;">
            <div>Discards the topmost history entry: <span
                    style="font-family: &quot;Courier New&quot;;"><font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">history.replaceState</font></span>
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="2" style="width: 190px; padding: 8px; border: 1px solid;">
            <div>Scroll Position</div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>If <span style="font-family: &quot;Courier New&quot;;">location</span> includes an
                anchor, attempts to scroll to the anchored element. Otherwise, it will scroll to the
                top of the page.
            </div>
        </td>
        <td style="width: 211px; padding: 8px; border: 1px solid;">
            <div>Saves scroll position before navigating away and automatically returns to this saved
                position on restoration visits.
            </div>
        </td>
    </tr>
    <tr>
        <td style="width: 190px; padding: 8px; border: 1px solid;" colspan="2">
            <div>Events &amp; Event listeners</div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">turbolinks:load</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> is fired i</span>n addition to
                standard:
            </div>
            <ul>
                <li>
                    <span style="font-family: &quot;Courier New&quot;;">window.onload</span>
                    
                </li>
                <li>
                    <span style="font-family: &quot;Courier New&quot;;"> DOMContentLoaded</span>
                    
                </li>
                <li>
                    
                        <a href="evernote:///view/117942769/s638/c01e60d5-cf95-406b-8b86-a50daa972f6b/c01e60d5-cf95-406b-8b86-a50daa972f6b/">jQuery's </a><a
                            href="evernote:///view/117942769/s638/c01e60d5-cf95-406b-8b86-a50daa972f6b/c01e60d5-cf95-406b-8b86-a50daa972f6b/"
                            style="font-family: &quot;Courier New&quot;;">ready</a>
                </li>
            </ul>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div><font face="Helvetica Neue">Can’t depend on a full page load to reset your
                environment every time you navigate.</font></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><font face="Helvetica Neue"><b>Handle before page change</b></font></div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>document<font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.addEventListener("turbolinks:before-visit"</font>,
                    function() {
                </div>
                <div>  // ...</div>
                <div>})</div>
            </div>
            <div/>
            <div><font face="Helvetica Neue"><b><br/></b></font></div>
            <div><b>Handle after page change</b></div>
            <div><b><br/></b></div>
            <div><span style="font-family: &quot;Courier New&quot;;">turbolinks:load</span> is fired
                again after every page change
            </div>
            <ul>
                <li>
                    Can be used as <span style="font-family: &quot;Courier New&quot;;">DOMContentLoaded</span> replacement
                    
                </li>
                <li>
                    Don’t use to add event listeners directly to elements on the page body: Use
                        event delegation to register event listeners once on <span
                                style="font-family: &quot;Courier New&quot;;">document</span> or <span
                                style="font-family: &quot;Courier New&quot;;">window</span>.
                    
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>document<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.addEventListener("turbolinks:load"</span>,
                    function() {<br/></div>
                <div>  // ...</div>
                <div>})</div>
            </div>
            <div/>
            <div><span style="font-family: Monaco;"><br/></span></div>
            <div><a href="https://github.com/turbolinks/turbolinks#full-list-of-events"><font
                    face="Helvetica Neue">Full list of events</font></a></div>
        </td>
        <td style="width: 211px; padding: 8px; border: 1px solid;">
            <div>Copies the page to cache using <span style="font-family: &quot;Courier New&quot;;">cloneNode(true)</span>,
                so when it’s returned from the cache, attached event listeners and associated data are
                discarded.
            </div>
            <div><br/></div>
            <div><span style="font-weight: bold;">Handle cache push</span></div>
            <div><br/></div>
            <div>E.g. Reset forms, Collapse expanded UI elements, Tear down third-party widgets</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>document<font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.addEventListener("turbolinks:before-cache"</font>,
                    function() {
                </div>
                <div>  // ...</div>
                <div>})</div>
            </div>
            <div/>
            <div><span style="font-weight: bold;"><br/></span></div>
            <div><span style="font-weight: bold;">Handle cache pop</span></div>
            <div><br/></div>
            <div>Turbolinks adds a <span style="font-family: &quot;Courier New&quot;;">data-turbolinks-preview</span>
                attribute to the <span
                        style="font-family: &quot;Courier New&quot;;">&lt;html&gt;</span> element when
                it displays a preview from cache.
            </div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>if (<font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">document.documentElement.hasAttribute("data-turbolinks-preview")</font>)
                    {
                </div>
                <div>  // Turbolinks is displaying a preview</div>
                <div>}</div>
            </div>
            <div/>
            <div/>
        </td>
    </tr>
    <tr>
        <td rowspan="5" style="height: 164px; width: 104px; padding: 8px; border: 1px solid;">
            <div>Rendering</div>
        </td>
        <td style="width: 86px; padding: 8px; border: 1px solid;">
            <div>General</div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>Will render a <span
                    style="font-weight: bold; color: rgb(255, 38, 0); --inversion-type-color: simple;">preview of the page</span><span
                    style="font-weight: bold;"/>from cache immediately after the visit starts, if
                possible
            </div>
        </td>
        <td style="width: 211px; padding: 8px; border: 1px solid;">
            <div>Renders <span
                    style="font-weight: bold; color: rgb(255, 38, 0); --inversion-type-color: simple;">copy of the page from cache</span>
                without making a request if possible<br/></div>
            <div/>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 86px; padding: 8px;">
            <div><font face="Courier New">window</font><font face="Helvetica Neue"> and </font><font
                    face="Courier New">document</font><font face="Helvetica Neue"> objects</font>
            </div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div>Reloaded from clean state</div>
        </td>
        <td colspan="2" style="width: 457px; padding: 8px; border: 1px solid;">
            <div><span
                    style="font-weight: bold; color: rgb(255, 38, 0); --inversion-type-color: simple;"><font
                    face="Helvetica Neue">Persisted (No change)</font></span></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><font face="Helvetica Neue">Retain their state across page changes, along with other
                objects you leave in memory.</font></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 86px; padding: 8px;">
            <div><font face="Courier New">&lt;html&gt;</font></div>
        </td>
        <td style="width: 167px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div><b><font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">Persisted
                (No change)</font></b></div>
            <div><b><font style="color: rgb(255, 38, 0); --inversion-type-color: simple;"><br/></font></b>
            </div>
            <div>Adds a <span
                    style="font-family: &quot;Courier New&quot;;">data-turbolinks-preview</span>
                attribute when displaying a preview from cache
            </div>
        </td>
        <td style="height: 92px; width: 211px; padding: 8px; border: 1px solid;" rowspan="3">
            <div><span style="font-weight: bold;">Replaced</span></div>
            <div><br/></div>
            <div><span style="font-size: 14px; font-weight: bold;">Merge current document contents with cached document</span>
            </div>
            <div><br/></div>
            <div>Mark elements as permanent so they’re not <span style="-en-paragraph:true;">reverted
</span> when the user navigates back (e.g. shopping cart counter):
            </div>
            <ul>
                <li>
                    Elements must have an id
                </li>
                <li>
                    Before each render, Turbolinks matches all permanent elements by id and
                        transfers them from the original page to the new page, preserving their data
                        and event listeners.
                    
                </li>
            </ul>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;div <font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">id="cart-counter"
                    data-turbolinks-permanent</font>&gt;1 item&lt;/div&gt;
                </div>
            </div>
            <div/>
            <div/>
            <div><b><br/></b></div>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 86px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">&lt;head&gt;</span></div>
        </td>
        <td rowspan="2" style="height: 56px; width: 167px; padding: 8px; border: 1px solid;">
            <div>Browsers loads and evaluates any <span style="font-family: &quot;Courier New&quot;;">&lt;script&gt;</span>
                elements 
            </div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div><b><font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">Merged</font></b>
            </div>
            <div><br/></div>
            <div>Appends any new <span
                    style="font-family: &quot;Courier New&quot;;">&lt;script&gt;</span> elements in
                the new page’s <span style="font-family: &quot;Courier New&quot;;">&lt;head&gt;</span>
                which aren’t in the current one (causing them to be loaded and evaluated) 
            </div>
            <div><br/></div>
            <div>Load your application’s JS bundle using <span
                    style="font-family: &quot;Courier New&quot;;">&lt;script&gt;</span> elements in
                the <span style="font-family: &quot;Courier New&quot;;">&lt;head&gt;</span> of your
                document to avoid reloading them with every page change.<br/></div>
            <ul>
                <li>
                    Use <span
                            style="font-family: &quot;Courier New&quot;;">&lt;script defer&gt;</span><font
                            face="Helvetica Neue"> instead of putting scripts at the end
                        of </font><span
                            style="font-family: &quot;Courier New&quot;;">&lt;body&gt;</span>
                </li>
            </ul>
            <div><br/></div>
            <div><span
                    style="font-size: 14px; font-weight: bold;">Force page reload if assets change</span>
            </div>
            <div><br/></div>
            <div>Track asset URLs in <span
                    style="font-family: &quot;Courier New&quot;;">&lt;head&gt;</span> between pages
                and automatically issue a full reload if they change
            </div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>&lt;head&gt;<br/></div>
                <div>  ...</div>
                <div>  &lt;link rel="stylesheet" href="/application-258e88d.css"<span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;"> data-turbolinks-track="reload"</span>&gt;
                </div>
                <div>  &lt;script src="/application-cbd3cd4.js" <span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">data-turbolinks-track="reload"</span>&gt;&lt;/script&gt;
                </div>
                <div>&lt;/head&gt;</div>
            </div>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 86px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">&lt;body&gt;</span></div>
        </td>
        <td style="width: 246px; padding: 8px; border: 1px solid;">
            <div>Replaced</div>
            <div><br/></div>
            <div>Evaluates <span style="font-family: &quot;Courier New&quot;;">&lt;script&gt;</span>
                elements in a page’s <span
                        style="font-family: &quot;Courier New&quot;;">&lt;body&gt;</span> each time it
                renders the page.
            </div>
            <div><br/></div>
            <div>Use inline body scripts to:</div>
            <ul>
                <li>
                    Set up per-page JavaScript state
                </li>
                <li>
                    Bootstrap client-side models
                </li>
            </ul>
            <div><br/></div>
            <div>Don’t use to (use the <span style="font-family: &quot;Courier New&quot;;">turbolinks:load</span>
                event instead):
            </div>
            <ul>
                <li>
                    Install behavior, or
                </li>
                <li>
                    Perform more complex operations when the page changes
                </li>
            </ul>
            <div><br/></div>
            <div>Annotate <span style="font-family: &quot;Courier New&quot;;">&lt;script&gt;</span>
                elements with <span style="font-family: &quot;Courier New&quot;;">data-turbolinks-eval="false"</span>
                if you do not want Turbolinks to evaluate them after rendering.
            </div>
            <ul>
                <li>
                    Won’t prevent your browser from evaluating scripts on the initial page
                        load.
                    
                </li>
            </ul>
            <div/>
        </td>
    </tr>
    </tbody>
</table>
