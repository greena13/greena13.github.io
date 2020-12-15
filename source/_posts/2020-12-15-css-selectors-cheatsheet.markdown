---
layout: post
title: "CSS Selectors Cheatsheet"
date: 2020-12-15 09:30:03 +0000
comments: true
categories: 
---

A CSS selectors cheatsheet using the concepts laid out in [Thinking in Dimensions: A Unified Approach to Filter grammars](/blog/2020/12/12/thinking-in-dimensions-a-unified-approach-to-filter-grammars).

<!--more-->

## Logic operators

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 74px;"/>
        <col style="width: 725px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 74px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">AND</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 725px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">selector1selector2</span></div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div># has classA AND classB</div>
                <div>.classA.classB</div>
                <div><br/></div>
                <div># Has classA and attributeA</div>
                <div>.classA[attributeA]</div>
                <div><br/></div>
                <div># Has attributeA and attributeB</div>
                <div>*[attributeA][attributeB]</div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 74px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">OR</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 725px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">selector1, selector2</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 74px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">NOT</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 725px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">:not(selector)</span></div>
        </td>
    </tr>
    </tbody>
</table>

## Filter operators

Note: See below for state-based pseudo-selectors.

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 103px;"/>
        <col style="width: 168px;"/>
        <col style="width: 173px;"/>
        <col style="width: 169px;"/>
        <col style="width: 200px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Semantic dimension</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 168px; padding: 8px;">
            <div>Element Hierarchy and content</div>
            <div>(Permutation)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 173px; padding: 8px;">
            <div>Element Position</div>
            <div>(Permutation)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div>Element type</div>
            <div>(Nominal)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div>Attribute</div>
            <div>(Permutation)</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">class</span> and <span
                    style="font-family: &quot;Courier New&quot;;">id</span> attributes are Fields separated by
                spaces
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 813px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Non anchored match</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Any / Presence</div>
        </td>
        <td colspan="2" rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 341px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(134, 134, 134);">Not really applicable (</span><span
                    style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Covered by the </span><span
                    style="color: rgb(134, 134, 134); --inversion-type-color:  simple; font-family: &quot;Courier New&quot;;">*</span><span
                    style="color: rgb(134, 134, 134); --inversion-type-color:  simple;"> wildcard)</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">*</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Includes </span><span
                    style="font-family: &quot;Courier New&quot;;">head, body</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">, etc</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">[attribute]</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Exact match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">element</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">[attribute='value’]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Complement:</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Attribute not equal:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">[name!=”value”]</span></div>
            <div><br/></div>
            <div><span
                    style="font-family: &quot;Helvetica Neue&quot;; font-weight: bold;">Field equality:</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">All elements with one of its classes exactly equal:</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">.class</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">First element with one of its ids exactly equal (</span>Document
                with multiple elements with the same id is invalid)<span
                        style="font-family: &quot;Helvetica Neue&quot;;">:</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">#id</span></div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="vertical-align: bottom; background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 813px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Top / Left anchored match</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Absolute match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 168px; padding: 8px;">
            <div>Document’s root element:</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:root</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Contents</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:first-line</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:first-letter</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Before any element that isn’t self-closing:</span>
            </div>
            <div>* content attribute is required, but can be left blank</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:before</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 173px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">:first-child</span></div>
            <div><br/></div>
            <div><span
                    style="font-family: &quot;Helvetica Neue&quot;;">Every two elements, starting at one (p</span>ositive
                numbers: infinite series)
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:nth-child(2n+1)</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;; font-style: italic;">Combination of element type exact match and and absolute relative position</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">The first child of a particular type for a parent: </span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:first-of-type</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:nth-of-type</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Starting with: </span><span
                    style="font-family: &quot;Courier New&quot;;">[attribute^='value’]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Contains prefix (equal to a given string or starting with that string followed by a hyphen (-).)</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">[name|=”value”]</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Immediate Relative match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 168px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Child: </span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">.list &gt; li</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 173px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Adjacent sibling (immediately proceeded by)</span><span
                    style="font-family: &quot;Courier New&quot;;">: </span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">h1 + p</span></div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not applicable (Nominal data)</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">(Thinking of each character in the substring as being the next immediate match of the one before):</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Contains: </span><span
                    style="font-family: &quot;Courier New&quot;;">[name*=”value”]</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Any relative match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 168px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Descendant:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">sel1 sel2</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 173px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Any following sibling:</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">h1 ~ p</span></div>
            <div><br/></div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div><span
                    style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not available</span>
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(204, 204, 204); width: 813px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Bottom / Right anchored match</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Absolute match</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 168px; padding: 8px;">
            <div>Content:</div>
            <div><br/></div>
            <div>After any element that isn’t self-closing</div>
            <div>* content attribute is required, but can be left blank</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:after</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 173px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">:last-child</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:nth-last-child</span></div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;; font-style: italic;">Combination of element type exact match and and relative position</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:last-of-type</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:nth-last-of-type</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Ends with: </span><span
                    style="font-family: &quot;Courier New&quot;;">[name$=”value”]</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Immediate Relative match</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 168px; padding: 8px;">
            <div>Content:</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:empty</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 173px; padding: 8px;">
            <div><span
                    style="font-family: &quot;Helvetica Neue&quot;;">Negative numbers: finite offset)</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:nth-child(-n+3)</span></div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(134, 134, 134);">Not applicable (Nominal data)</span>
            </div>
        </td>
        <td rowspan="2"
            style="height: 56px; border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(134, 134, 134);">Not really applicable - just the reverse of the left/anchor match</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Any relative match</span>
            </div>
        </td>
        <td colspan="2" style="border: 1px solid rgb(204, 204, 204); width: 341px; padding: 8px;">
            <div><span
                    style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not available</span>
            </div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 813px; padding: 8px;">
            <div><span style="color: rgb(0, 0, 0); --inversion-type-color:  simple;">Projections</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Exclusive   (unique) match</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 168px; padding: 8px;">
            <div><span style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not really applicable (and/or covered by the position dimension instead)</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 173px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Combines position and count: </span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">All elements of for which they’re the only child of their parent: </span>
            </div>
            <div><span style="font-family: &quot;Courier New&quot;;">:only-child</span></div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Combines position and type, and then count:</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">All elements for which they’re the only of their type in their parent: </span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:only-of-type</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 200px; padding: 8px;">
            <div><span
                    style="color: rgb(134, 134, 134); --inversion-type-color:  simple;">Not available</span>
            </div>
        </td>
    </tr>
    </tbody>
</table>

### State-based pseudo-selectors

State-based pseudo-selectors filter on the set of virtual flags (nominal data) that keep track of element's state in the DOM.

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 139px;"/>
        <col style="width: 668px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;"
            colspan="2">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">User/Mouse</span></div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:link</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Unvisited links</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:hover</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Mouse over</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:visited</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Visited links</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:active</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Element is being activated (e.g. mid-click)</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:focus</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Focus via click or keyboard event</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:selection</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Part of an element that is </span><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">selected by the user</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-size: 14px; font-family: &quot;Courier New&quot;;">:target</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Selects the element matching the URI target</span>
            </div>
            <div style="--inversion-type-color: simple;"><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div><span style="color: rgb(41, 41, 41); --inversion-type-color:  simple;"><a
                        href="https://example.com/#foo">https://example.com/#foo</a></span></div>
                <div><br/></div>
                <div><span style="color: rgb(41, 41, 41); --inversion-type-color:  simple;">$("p:target”) # Selects &lt;p id="foo"&gt;</span>
                </div>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;"
            colspan="2">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Form elements</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:default</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Elements in their default state</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0); font-family: &quot;Courier New&quot;;">:enabled</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Elements that have their boolean </span><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0); font-family: &quot;Courier New&quot;;">disabled</span><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0);"> property strictly equal to false</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:disabled</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Disabled elements</span>
            </div>
            <ul>
                <li>
                    <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">matches elements that are </span><a
                            href="https://html.spec.whatwg.org/multipage/semantics-other.html#disabled-elements"
                            style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">actually
                        disabled</a><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);"> while </span><span
                            style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">[disabled]</span><span
                            style="--inversion-type-color:  simple; color: rgb(41, 41, 41);"> only checks for the existence of the disabled attribute</span>
                    </div>
                </li>
                <li>
                    <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">should only be used for selecting HTML elements that support the disabled attribute (</span><span
                            style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">&lt;button&gt;, &lt;input&gt;, &lt;optgroup&gt;, &lt;option&gt;, &lt;select&gt;, &lt;textarea&gt;, &lt;menuitem&gt;</span><span
                            style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">, and </span><span
                            style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">&lt;fieldset&gt;</span><span
                            style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">)</span></div>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:optional</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Elements with no "required" attribute</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:invalid</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Invalid elements</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:read-only</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Elements with “readonly” attribute</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:read-write</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Elements without “readonly” attribute</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:enabled</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Elements not in a disabled state</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:in-range</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Applies to elements that have range limitations</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0); font-family: &quot;Courier New&quot;;">:checked</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Works for checkboxes, radio buttons, and options of select elements.</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:out-of-range</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 807px; padding: 8px;"
            colspan="2">
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Localisation</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 139px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">:lang</span>
            </div>
        </td>
        <td style="background-color: rgb(255, 255, 255); border: 1px solid rgb(219, 219, 219); width: 668px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0); font-size: 14px; font-family: &quot;Helvetica Neue&quot;;">Selects elements with matching </span><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0); font-size: 14px; font-family: &quot;Courier New&quot;;">lang=“*</span><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0); font-size: 14px; font-family: &quot;Helvetica Neue&quot;;">’</span><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0); font-size: 14px; font-family: &quot;Helvetica Neue&quot;;"> attribute</span>
            </div>
        </td>
    </tr>
    </tbody>
</table>
