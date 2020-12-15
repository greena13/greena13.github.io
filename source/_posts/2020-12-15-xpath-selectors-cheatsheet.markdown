---
layout: post
title: "XPath Selectors Cheatsheet"
date: 2020-12-15 09:53:04 +0000
comments: true
categories: 
---

A XPath selectors cheatsheet using the concepts laid out in [Thinking in Dimensions: A Unified Approach to Filter grammars](/blog/2020/12/12/thinking-in-dimensions-a-unified-approach-to-filter-grammars).

<!--more-->

## Logic operators
                    
XPath provides some of its logic operators both inside expressions and predicates (sub-filters). 

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 105px;"/>
        <col style="width: 354px;"/>
        <col style="width: 328px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 105px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 354px; padding: 8px;">
            <div>Inside expression</div>
        </td>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 328px; padding: 8px;">
            <div>Inside predicate</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 105px; padding: 8px;">
            <div>Equal</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 354px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//a[@id </span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">=</span><span
                    style="font-family: &quot;Courier New&quot;;"> "xyz"]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 328px; padding: 8px;">
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 105px; padding: 8px;">
            <div>Not equal</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 354px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//a[@id </span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">!= </span><span
                    style="font-family: &quot;Courier New&quot;;">"xyz"]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 328px; padding: 8px;">
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 105px; padding: 8px;">
            <div>Greater than</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 354px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//a[@price </span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">&gt;</span><span
                    style="font-family: &quot;Courier New&quot;;"> 25]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 328px; padding: 8px;">
            <div><br/></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 105px; padding: 8px;">
            <div>AND</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 354px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//div[@class="head"]</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">[@id="top"]</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 328px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//div[@id="head" </span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">and</span><span
                    style="font-family: &quot;Courier New&quot;;"> position()=2]</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 105px; padding: 8px;">
            <div>OR</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 354px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//a | //div</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 328px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//div[(x and y) </span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">or</span><span
                    style="font-family: &quot;Courier New&quot;;"> not(z)]</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 105px; padding: 8px;">
            <div>NOT</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 354px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 328px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//div[(x and y) or </span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">not</span><span
                    style="font-family: &quot;Courier New&quot;;">(z)]</span></div>
        </td>
    </tr>
    </tbody>
</table>

## Filtering vs selection

XPath provides a more flexible set of selection options than CSS. 

XPath expressions allow filtering nodes based on their ancestors or children without changing the selection target.

```
# Selects descendant
//ul/li[1]

# Select parent based on descendant
//ul[li[position()=1]]
```
                            
XPath also allows matching or returning element attributes (as opposed to the whole element):


```
# Filter on attribute
//button[text()="Submit”]

# Match or return the attribute value
//button/text()
```
           
## Semantic dimensions

XPath can be used for XML or HTML documents.

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 103px;"/>
        <col style="width: 167px;"/>
        <col style="width: 174px;"/>
        <col style="width: 169px;"/>
        <col style="width: 199px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div>Element Hierarchy and content</div>
            <div>(Permutation)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div>Element Position</div>
            <div>(Permutation)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div>Element type</div>
            <div>(Nominal)</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
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
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 812px; padding: 8px;"
            colspan="5">
            <div><span style="color: rgb(0, 0, 0); --inversion-type-color:  simple;">Non anchored match</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Any / Presence</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div>Anywhere:</div>
            <div><font face="Courier New"><br/></font></div>
            <div><font
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">//</font><font
                    face="Courier New">hr</font></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><font face="Helvetica Neue">Self:</font></div>
            <div><font face="Helvetica Neue"
                       style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.</font></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><font face="Helvetica Neue">Short for: </font><span
                    style="font-family: &quot;Courier New&quot;;">self::node()</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">*</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">*</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
            <div><font face="Helvetica Neue">Attribute:</font></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//a[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">@</font>rel]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">a[rel]</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Exact match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Match Content:</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//button[</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">text()=</span><span
                    style="font-family: &quot;Courier New&quot;;">"Submit”]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Return Content:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//span/text()</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;"><br/></td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">//</span><span
                    style="font-family: &quot;Courier New&quot;;">h1</span></div>
            <div><br/></div>
            <div>CSS Equivalent:</div>
            <div><span style="font-family: &quot;Courier New&quot;;">h1</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//input[@type="submit"]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">input[type="submit"]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Return attribute value:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//a</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">/@</span><span
                    style="font-family: &quot;Courier New&quot;;">href</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Match on language:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">lang(str)</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Matching on </span><span
                    style="font-family: &quot;Courier New&quot;;">id</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;"> and </span><span
                    style="font-family: &quot;Courier New&quot;;">class</span><span
                    style="font-family: &quot;Helvetica Neue&quot;;">: </span></div>
            <div><br/></div>
            <div>No fields operator, so need workaround:</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//div[contains(concat(' ',normalize-space(@class),' '),' foobar ')]</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">.foobar</span></div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="vertical-align: bottom; background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 812px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Top / Left anchored match</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Absolute match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple;"><font face="Helvetica Neue"
                                                                      style="color: rgb(41, 41, 41); --inversion-type-color: simple;">Root</font></span>
            </div>
            <div><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">/</span>
            </div>
            <div><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">/body</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:root</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><font face="Helvetica Neue">Match content:</font></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//[</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">starts-with(</span><span
                    style="font-family: &quot;Courier New&quot;; --inversion-type-color:  simple;"><font
                    style="color: rgb(41, 41, 41); --inversion-type-color: simple;">text</font></span><span
                    style="font-family: &quot;Courier New&quot;; --inversion-type-color:  simple; color: rgb(41, 41, 41);">()</span><span
                    style="font-family: &quot;Courier New&quot;;">, 'h'</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//ul/li</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">[1]</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//ul/li[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">position()=</font>1]</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">ul &gt; li:first-child</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">starts-with(</font></span><span
                    style="font-family: &quot;Courier New&quot;; --inversion-type-color:  simple;"><font
                    style="color: rgb(41, 41, 41); --inversion-type-color: simple;">name()</font></span><span
                    style="font-family: &quot;Courier New&quot;;">, 'h'<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</font>]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//a[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">starts-with(</font>@href, '/‘<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</font>]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">a[href^='/']</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Immediate Relative match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Match on child type:</span></div>
            <div><span style="--inversion-type-color:  simple; font-family: &quot;Courier New&quot;;"><font
                    style="color: rgb(41, 41, 41); --inversion-type-color: simple;">//</font></span><span
                    style="font-family: &quot;Courier New&quot;;">ul</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">/</span><span
                    style="font-family: &quot;Courier New&quot;;">li</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Short for:</span><span
                    style="font-family: &quot;Courier New&quot;;"> //ul/child::li</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div>CSS Equivalent:</div>
            <div><span style="font-family: &quot;Courier New&quot;;">ul &gt; li</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Content:</span></div>
            <div><br/></div>
            <div><font face="Courier New">//button[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">contains(</font>text(),”Submit”<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</font>]</font></div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Adjacent sibling (immediately proceeded by)</span><span
                    style="font-family: &quot;Courier New&quot;;">: </span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//h1/</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">following-sibling::</span><span
                    style="font-family: &quot;Courier New&quot;;">ul</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">[1]</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">h1 + ul</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//[</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">contains(</span><span
                    style="font-family: &quot;Courier New&quot;; --inversion-type-color:  simple; color: rgb(41, 41, 41);">name()</span><span
                    style="font-family: &quot;Courier New&quot;;">, 'h'</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">(Thinking of each character in the substring as being the next immediate match of the one before):</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">a[href</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">*=</span><span
                    style="font-family: &quot;Courier New&quot;;">'://‘]</span></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Or</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">a[href</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">~</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">=</span><span
                    style="font-family: &quot;Courier New&quot;;">'://‘]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">font[</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">contains(@class,"head")</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
            <div><br/></div>
            <div>CSS Equivalent:</div>
            <div><span style="font-family: &quot;Courier New&quot;;">//a[contains(@href, </span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">'://')]</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Any relative match</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div>Descendant or self</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//div</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">//</span><span
                    style="font-family: &quot;Courier New&quot;;">p</span></div>
            <div><br/></div>
            <div>Short for:<span style="font-family: &quot;Courier New&quot;;"> </span></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">/descendant</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">-or-self</span><span
                    style="font-family: &quot;Courier New&quot;;">::node()/</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><font face="Helvetica Neue">Any descendant</font></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">//div/descendant::</font>p/</span>
            </div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div>CSS Equivalent:</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">div p</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Any following sibling:</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//h1/</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">following-sibling::</span><span
                    style="font-family: &quot;Courier New&quot;;">ul</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">h1 ~ ul</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div>Combines position and hierarchy:</div>
            <div><br/></div>
            <div>Everything in the document after the closing tag of the current node</div>
            <div><br/></div>
            <div><font face="Courier New" style="color: rgb(255, 38, 0); --inversion-type-color: simple;">/following</font>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><font face="Helvetica Neue">Matches tag types with ‘h’ after ‘1’</font></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><font style="font-family: &quot;Courier New&quot;;">//[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">contains(substring-after(</font>name(), “h”<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</font>, “1”<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</font>]</font></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
            <div><span style="font-family: Helvetica Neue;">Matches attributes with ids with ‘bar’ after ‘foo’:</span>
            </div>
            <div><span style="font-family: Helvetica Neue;"><br/></span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//[</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">contains(substring-after(</span><font
                    face="Courier New">@id, “foo”</font><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span><span
                    style="font-family: &quot;Courier New&quot;;">, “bar”</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
        </td>
    </tr>
    <tr>
        <td colspan="5"
            style="background-color: rgb(234, 234, 234); border: 1px solid rgb(204, 204, 204); width: 812px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Bottom / Right anchored match</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Absolute match</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div>Content:<br/></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//[</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">ends-with(</span><span
                    style="font-family: &quot;Courier New&quot;; --inversion-type-color:  simple; color: rgb(41, 41, 41);">text()</span><span
                    style="font-family: &quot;Courier New&quot;;">, 'h'</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">/li[last()]</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//ul/li[</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">position()= last()</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">:last-child</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">ends-with(</font></span><span
                    style="font-family: &quot;Courier New&quot;; --inversion-type-color:  simple;"><font
                    style="color: rgb(41, 41, 41); --inversion-type-color: simple;">name()</font></span><span
                    style="font-family: &quot;Courier New&quot;;">, 'h'<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</font>]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">//a[</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">ends-with(</span><span
                    style="font-family: &quot;Courier New&quot;;">@href, '.pdf’</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">)</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">CSS Equivalent:</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">a[href$='pdf']</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Immediate Relative match</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div>Parent:</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">..</font></span></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><font face="Helvetica Neue">Short for: </font><span
                    style="font-family: &quot;Courier New&quot;;">parent::node()</span></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;; font-weight: bold;"><br/></span></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;; font-weight: bold;">Has children</span>
            </div>
            <div><br/></div>
            <div><span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);">Single match: </span>
            </div>
            <div><span style="font-family: &quot;Courier New&quot;;">//ul</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">[*]</span>
            </div>
            <div><span style="font-family: &quot;Courier New&quot;;">//ul</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">[li]</span><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;"> </span>
            </div>
            <div><br/></div>
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">Match per child:</span>
            </div>
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">//ul</span><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">/li</span><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">/..</span>
            </div>
            <div><br/></div>
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">jQuery Equivalent:</span>
            </div>
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">$('ul &gt; li').parent()</span>
            </div>
            <div><br/></div>
            <div><span
                    style="color: rgb(41, 41, 41); --inversion-type-color:  simple; font-family: &quot;Helvetica Neue&quot;;">Has certain number of children:</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//table[count(tr)=1]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Adjacent sibling (immediately proceeding)</span><span
                    style="font-family: &quot;Courier New&quot;;">: </span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//h1/</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">proceeding-sibling::</span><span
                    style="font-family: &quot;Courier New&quot;;">ul</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">[1]</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 368px; padding: 8px;" colspan="2">
            <div><font style="color: rgb(134, 134, 134); --inversion-type-color: simple;">Same as Left/Top
                anchor match, reversed</font></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div><span style="--inversion-type-color:  simple; color: rgb(0, 0, 0);">Any relative match</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div>Closest ancestor (or self) matching selector:</div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//ul/</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">ancestor-or-self::li</span>
            </div>
            <div><br/></div>
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Helvetica Neue&quot;;">jQuery Equivalent:</span>
            </div>
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;">$('li').closest('section’)</span>
            </div>
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(41, 41, 41); font-family: &quot;Courier New&quot;;"><br/></span>
            </div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">All proceeding siblings</span><span
                    style="font-family: &quot;Courier New&quot;;">: </span></div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//h1/</span><span
                    style="--inversion-type-color: simple; color: rgb(255, 38, 0); font-family: &quot;Courier New&quot;;">proceeding-sibling::</span><span
                    style="font-family: &quot;Courier New&quot;;">ul</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><font face="Helvetica Neue">Combines position and hierarchy:</font></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div>Everything in the document before the opening tag of the current node</div>
            <div><br/></div>
            <div><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">/preceding</span>
            </div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><span
                    style="font-family: &quot;Helvetica Neue&quot;;">Matches tag types with ‘h’ before ‘1’</span>
            </div>
            <div><br/></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//[</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">contains(substring-before(</span><span
                    style="font-family: &quot;Courier New&quot;;">name(), “1”</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span><span
                    style="font-family: &quot;Courier New&quot;;">, “h”</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
            <div><font style="font-family: &quot;Helvetica Neue&quot;;">Matches attributes with ids with ‘foo’
                before ‘bar’:</font></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><span style="font-family: Courier New;">//[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">contains(substring-before(</font>@id, “bar”), “foo”<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)]</font></span></div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 812px; padding: 8px;"
            colspan="5"><font style="color: rgb(0, 0, 0); --inversion-type-color: simple;">Projections</font>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 103px; padding: 8px;">
            <div>Size or count</div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 167px; padding: 8px;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Element with single child:</span>
            </div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;"><br/></span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//table[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">count(tr)</font></span>
            </div>
            <div><span style="font-family: &quot;Courier New&quot;;"><font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">=1</font>]</span></div>
            <div><span style="font-family: &quot;Courier New&quot;;"><br/></span></div>
            <div><span style="font-family: &quot;Courier New&quot;;">//table[</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">count(tr)</span>
            </div>
            <div><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">&gt; 1</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 174px; padding: 8px;">
            <div><br/></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 169px; padding: 8px;">
            <div><font style="font-family: &quot;Helvetica Neue&quot;;">Match all tags with a type that is 2
                characters long:</font></div>
            <div><font face="Helvetica Neue"><br/></font></div>
            <div><span style="font-family: &quot;Courier New&quot;;">/</span><span
                    style="font-family: &quot;Courier New&quot;;">/[</span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">string-length(</span><span
                    style="font-family: &quot;Courier New&quot;; --inversion-type-color:  simple;"><font
                    style="color: rgb(41, 41, 41); --inversion-type-color: simple;">name()</font></span><span
                    style="font-family: &quot;Courier New&quot;; color: rgb(255, 38, 0); --inversion-type-color: simple;">) = 2)</span><span
                    style="font-family: &quot;Courier New&quot;;">]</span></div>
        </td>
        <td style="border: 1px solid rgb(204, 204, 204); width: 199px; padding: 8px;">
            <div><span style="font-family: Helvetica Neue;">Match all tags with an id that is 2 characters long:</span>
            </div>
            <div><span style="font-family: Helvetica Neue;"><br/></span></div>
            <div><font style="font-family: &quot;Courier New&quot;;">//[<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">string-length(</font>@id<font
                    style="color: rgb(255, 38, 0); --inversion-type-color: simple;">) = 2)</font>]</font>
            </div>
        </td>
    </tr>
    </tbody>
</table>


## Other Projections
                
Some other projection functions that may not be covered above:

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 809px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 809px; padding: 8px;">
            Accessors
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(187, 187, 187); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">lang(str)</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(187, 187, 187); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">namespace-uri()</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">concat(x,y)</span></div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 809px; padding: 8px;">
            <div><font style="color: rgb(0, 0, 0); --inversion-type-color: simple;">String projections</font>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">substring(str, start, len)</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">substring-before("01/02", "/")</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">substring-after("01/02", "/")</span>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">translate()</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">normalize-space()</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">string-length()</span></div>
        </td>
    </tr>
    <tr>
        <td style="background-color: rgb(234, 234, 234); border: 1px solid rgb(187, 187, 187); width: 809px; padding: 8px;">
            <div><font style="color: rgb(0, 0, 0); --inversion-type-color: simple;">Type conversion</font>
            </div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">string()</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">number()</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid rgb(204, 204, 204); width: 809px; padding: 8px;">
            <div><span style="font-family: &quot;Courier New&quot;;">boolean()</span></div>
        </td>
    </tr>
    </tbody>
</table>

