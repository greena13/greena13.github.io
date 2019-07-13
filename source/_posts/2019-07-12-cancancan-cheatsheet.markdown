---
layout: post
title: "CanCanCan Cheatsheet"
date: 2019-07-12 08:14:48 +0100
comments: true
categories: 
---

A breakdown of how to define and check abilities with CanCanCan, with an opinionated set of best practices based on experience.
<!--more-->

## Defining abilities

### General Best practices 

#### No access by default

Give abilities as you discover you need them 

* Don’t define them upfront because you think you’ll need them
* Always use can? to enable a feature - never use cannot? to take it away

Only use the standard `index`, `show`, `create`, `update`, `destroy` (edit is subset of updated and new is a subset of create)

#### Your ability file is not somewhere to be DRY; being explicit and clear

Don’t use `manage` (its too permissive)

Don’t define aliases - always be explicit and granular:

```ruby
# Bad
alias_action :create, :read, :update, :destroy, to: :crud
can :crud, User

# Good
can [:create, :read, :update, :destroy], User
```

Define permissions for one resource at a time.

```ruby
# Bad: Update or destroy both articles and comments
can [:update, :destroy], [Article, Comment]

# Good
can [:update, :destroy], Article
can [:update, :destroy], Comment
```

### Syntax

#### Hash Conditions

Preferred syntax (used most consistently without hidden side-effects between all 3 methods)
* can? & authorize!: Keys match methods on instance, which must return values equal to the hash's values
* accessible_by: Generates a LEFT JOIN to query conditions on associated records (so almost anything you can pass to a hash of conditions in Active Record will work)

```ruby
can :read, Project, active: true, user_id: user.id
```

Can’t pass model instances into the hash - you must use their id instead:

```ruby
# Won’t work
can :manage, Project, group: user.groups

# Will work
can :manage, Project, group: { id: user.group_ids }
```

Must use lower-level attributes rather than your existing scopes that wrap them:

```ruby
can :read, Article, is_published: true
```

Can use a range:

```ruby
can :read, Project, priority: 1..3
```

Defining conditions on associations:
* Will issue a query that performs an `LEFT JOIN` to query conditions on associated records

```ruby
can :read, Project, category: { visible: true }
can :manage, Part, service: { account: { user: { id: user.id } } }
```

#### Blocks

Use only if you need to pass additional context or can’t express the logic using hashes:

```ruby
can :create, Project do |project, remote_ip|
  # ...
end

can? :create, Project, request.remote_ip
```

Only evaluated when an actual instance object is present

```ruby
can :update, Project do |project|
  project.priority < 3
end
```

#### Scopes & Raw SQL

If you need an operator other than the equality one, or for other complex cases, you can provide a scope or a Raw SQL segment.

You must **always also define a block**, or you get an error:

```ruby
can :read, Article, Article.published_recently

# Raises exception:
# The can? and cannot? call cannot be used with a raw sql 'can' definition.
# The checking code cannot be determined for :read #<Article ..>.
can? :read, Article
```    

When you also define a block:

* Block is used when `can?` and `authorize!` are passed an instance
* Scope or Raw SQL segment is use when `accessible_by` is called
* Neither are used when when `can?` and `authorize!` are passed a class (only matches on action and class type)

```ruby
can :read, Article, Article.published_recently do |article|
  article.created_at > DateTime.now - 3.days
end

# Works fine
can? :read, Article
```

Using a SQL segment:

```ruby
can :update, Project, ["priority < ?", 3] do |project|
  project.priority < 3
end
```

#### Operators

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 62px;"/>
        <col style="width: 274px;"/>
        <col style="width: 294px;"/>
        <col style="width: 93px;"/>
        <col style="width: 88px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td rowspan="2" style="height: 56px; width: 62px; padding: 8px; border: 1px solid;">
            <div>Operator</div>
        </td>
        <td rowspan="2" style="height: 56px; width: 274px; padding: 8px; border: 1px solid;">
            <div>Actions</div>
        </td>
        <td colspan="3" style="width: 475px; padding: 8px; border: 1px solid;">
            <div>Conditions</div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 294px; padding: 8px;">
            <div>Hash</div>
        </td>
        <td style="width: 93px; padding: 8px; border: 1px solid;">
            <div>Block</div>
        </td>
        <td style="width: 88px; padding: 8px; border: 1px solid;">
            <div>Raw SWL</div>
        </td>
    </tr>
    <tr>
        <td style="width: 62px; padding: 8px; border: 1px solid;">
            <div>AND</div>
        </td>
        <td style="width: 274px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>can <font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">[</font>:show<font
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">,</font>
                    :index<font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">]</font>
                </div>
            </div>
            <div/>
        </td>
        <td style="width: 294px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>can :read, Article, <span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">author_id: @user.id, is_published: false</span><br/>
                </div>
                <div><br clear="none"/></div>
                <div>WHERE ( `articles`.`author_id` = 97 AND `articles`.`is_published` = 0 )</div>
            </div>
        </td>
        <td rowspan="3" style="height: 92px; width: 93px; padding: 8px; border: 1px solid;">
            <div>Normal Ruby syntax</div>
            <div/>
        </td>
        <td rowspan="3" style="height: 92px; width: 88px; padding: 8px; border: 1px solid;">
            <div>Normal SQL syntax</div>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="width: 62px; padding: 8px; border: 1px solid;">
            <div>OR</div>
        </td>
        <td style="width: 274px; padding: 8px; border: 1px solid;">
            <div>Read released or preview:</div>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>can :read, Project, released: true</div>
                <div><font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">can :read,
                    Project, preview: true</font></div>
            </div>
            <div/>
        </td>
        <td rowspan="2" style="height: 56px; width: 294px; padding: 8px; border: 1px solid;">
            <div>Not possible</div>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="width: 62px; padding: 8px; border: 1px solid;">
            <div>NOT</div>
        </td>
        <td style="width: 274px; padding: 8px; border: 1px solid;">
            <div>Not recommended - always give abilities, don’t take them away</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div><font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">cannot</font>
                    :destroy, Project
                </div>
            </div>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="width: 62px; padding: 8px; border: 1px solid;">
            <div>Precedence</div>
        </td>
        <td style="width: 274px; padding: 8px; border: 1px solid;">
            <div>An ability rule will override a previous one:</div>
            
            <div>
                <span>If they were reversed,</span>
                <span>cannot :destroy</span>
                <span> would be overridden by </span>
                <span>can :manage</span>
                <span>.</span>
            </div>
                                        
            <div/>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>can :manage, Project</div>
                <div>cannot :destroy, Project</div>
            </div>
            <div/>
        </td>
        <td style="width: 294px; padding: 8px; border: 1px solid;">
            <div>Equal</div>
            <div/>
        </td>
        <td style="width: 93px; padding: 8px; border: 1px solid;">
            <div>Normal Ruby precedence</div>
        </td>
        <td style="width: 88px; padding: 8px; border: 1px solid;">
            <div>Depends on your database</div>
        </td>
    </tr>
    </tbody>
</table>

#### Defining custom logic

Useful when permissions are defined outside of Ruby such as when defining Abilities in Database
* Block will be triggered for every `can?` check, even when only a class is used in the check.

```ruby
can do |action, subject_class, subject|
  # ...
end
```

### Merging ability files

```ruby
# ApplicationController.rb
def current_ability
  @current_ability ||= ReadAbility.new(current_user).merge(WriteAbility.new(current_user))
end    
```

## Checking abilities

### General best practices
          
Never use `load_and_authorize_resource` or `load_resource`, instead use the more explicit `authorize!` call for readability and flexibiltiy

Use `check_authorization` to ensure all controller actions are authorized:
* Adds an `after_filter` to ensure `authorize!` is called for every controller action 

```ruby
class ApplicationController < ActionController::Base
  check_authorization

  skip_authorization_check :only => [:new, :create]

  check_authorization unless: :admin_subdomain? # Only skips checking authorization - still does the authorization
  
  private
  
  def admin_subdomain?
    request.subdomain == "admin"
  end
end
```

### CanCanCan methods

There are 3 methods for checking a user's abilities:

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 122px;"/>
        <col style="width: 227px;"/>
        <col style="width: 205px;"/>
        <col style="width: 250px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td rowspan="1" style="width: 122px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 227px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">can?</span></div>
        </td>
        <td rowspan="1" style="width: 205px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">authorize!</span></div>
        </td>
        <td rowspan="1" style="width: 250px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">accessible_by</span></div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 122px; padding: 8px; border: 1px solid;">
            <div>For</div>
        </td>
        <td colspan="2" style="width: 432px; padding: 8px; border: 1px solid;">
            <div>Checking user has ability to do an action on a resource</div>
        </td>
        <td rowspan="1" style="width: 250px; padding: 8px; border: 1px solid;">
            <div>Filtering a collection down to those resources a user has the ability to perform an action on
                (Default: <span style="font-family: &quot;Courier New&quot;;">:index</span>)
            </div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 122px; padding: 8px; border: 1px solid;">
            <div>Input</div>
        </td>
        <td colspan="2" style="width: 432px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">(action, class|instance[, additional_block_args])</span>
            </div>
        </td>
        <td rowspan="1" style="width: 250px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">accessible_by(current_ability[, action = :index])</span>
            </div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 122px; padding: 8px; border: 1px solid;">
            <div>Output</div>
        </td>
        <td style="width: 227px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Returns</span><span
                    style="font-family: &quot;Courier New&quot;;"> true/false</span></div>
        </td>
        <td rowspan="1" style="width: 205px; padding: 8px; border: 1px solid;">
            <div>Throws <span style="font-family: &quot;Courier New&quot;;">CanCan::AccessDenied</span>
                exception
            </div>
        </td>
        <td rowspan="1" style="width: 250px; padding: 8px; border: 1px solid;">
            <div>Returns scoped collection</div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 122px; padding: 8px; border: 1px solid;">
            <div>Application</div>
        </td>
        <td style="width: 227px; padding: 8px; border: 1px solid;">
            <div><span
                    style="font-family: &quot;Helvetica Neue&quot;;">Views (for toggling UI elements)</span>
            </div>
        </td>
        <td rowspan="1" style="width: 205px; padding: 8px; border: 1px solid;">
            <div>Controller actions (for rescuing exceptions and rendering unauthorized)</div>
        </td>
        <td rowspan="1" style="width: 250px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">index</span> action for filtering
                resources list
            </div>
            <div><br clear="none"/></div>
            <div>Elsewhere for filtering associations</div>
        </td>
    </tr>
    </tbody>
</table>

Abilities are matched on up to 3 different attributes:

* An action type
* A target or subject class
* A set of conditions (optional, if defined)

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 111px;"/>
        <col style="width: 77px;"/>
        <col style="width: 73px;"/>
        <col style="width: 114px;"/>
        <col style="width: 156px;"/>
        <col style="width: 288px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 111px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div>Required for Definition</div>
        </td>
        <td colspan="4" style="width: 631px; padding: 8px; border: 1px solid;">
            <div>Required to match for check to pass</div>
        </td>
    </tr>
    <tr>
        <td style="width: 111px; padding: 8px; border: 1px solid;">
            <div>Action Type</div>
        </td>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div>Yes</div>
        </td>
        <td colspan="4" style="width: 631px; padding: 8px; border: 1px solid;">
            <div>Always</div>
        </td>
    </tr>
    <tr>
        <td style="width: 111px; padding: 8px; border: 1px solid;">
            <div>Target or Subject Type</div>
        </td>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div>Yes</div>
        </td>
        <td colspan="4" style="width: 631px; padding: 8px; border: 1px solid;">
            <div>Always</div>
        </td>
    </tr>
    <tr>
        <td rowspan="3" style="height: 92px; width: 111px; padding: 8px; border: 1px solid;">
            <div>Conditions</div>
            <div><br/></div>
            <div><br/></div>
        </td>
        <td rowspan="3" style="height: 92px; width: 77px; padding: 8px; border: 1px solid;">
            <div>No</div>
            <div><br/></div>
            <div><br/></div>
        </td>
        <td rowspan="1" style="width: 73px; padding: 8px; border: 1px solid;">
            <div>Target</div>
        </td>
        <td rowspan="1" style="width: 114px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">can?</span></div>
        </td>
        <td rowspan="1" style="width: 156px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">authorize!</span></div>
        </td>
        <td rowspan="1" style="width: 288px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">accessible_by</span></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 73px; padding: 8px;">
            <div>Instance</div>
        </td>
        <td colspan="2" rowspan="1" style="width: 270px; padding: 8px; border: 1px solid;">
            <div><span style="color: rgb(41, 41, 41); --inversion-type-color: simple;">Matches:</span><br/>
            </div>
            <ul>
                <li>
                    <font
                            style="color: rgb(255, 38, 0); --inversion-type-color: simple;">Block</font><font
                            style="color: rgb(41, 41, 41); --inversion-type-color: simple;"> if defined
                        (evaluate to true/false)</font>
                </li>
            </ul>
            <ul>
                <li>
                    <font
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">Hash</font><span
                        style="color: rgb(41, 41, 41); --inversion-type-color: simple;"> (if no block defined) Compares values of condition hash against those returned by calling the methods on the target with the same name as the corresponding condition hash key</span>
                </li>
            </ul>
            <div><span style="color: rgb(41, 41, 41); --inversion-type-color: simple;"><br/></span></div>
            <div><span style="color: rgb(41, 41, 41); --inversion-type-color: simple;">Ignores: </span></div>
            <ul>
                <li>
                    <span style="color: rgb(41, 41, 41); --inversion-type-color: simple;">Raw SQL or Scope (</span><span
                            style="color: rgb(41, 41, 41); --inversion-type-color: simple;">Throws error if block isn’t also defined)</span>
                </li>
            </ul>
        </td>
        <td style="width: 288px; padding: 8px; border: 1px solid;">
            <div><font style="color: rgb(110, 110, 110); --inversion-type-color: simple;">Not
                Applicable</font></div>
        </td>
    </tr>
    <tr>
        <td style="border: 1px solid; width: 73px; padding: 8px;">
            <div>Class</div>
        </td>
        <td colspan="2" style="width: 270px; padding: 8px; border: 1px solid;">
            <div><font style="color: rgb(110, 110, 110); --inversion-type-color: simple;">Ignored. Only ever
                matches on action ant target.</font></div>
            <div><font style="color: rgb(110, 110, 110); --inversion-type-color: simple;"><br/></font></div>
            <div><font style="color: rgb(110, 110, 110); --inversion-type-color: simple;">(Raw SQL or scope
                still throws error if block isn’t also defined)</font></div>
        </td>
        <td rowspan="1" style="width: 288px; padding: 8px; border: 1px solid;">
            <div><span style="color: rgb(41, 41, 41); --inversion-type-color:  simple;">Matches:</span></div>
            <ul>
                <li>
                    <span style="--inversion-type-color:  simple; color: rgb(41, 41, 41);"><font
                            style="color: rgb(255, 38, 0); --inversion-type-color: simple;">Raw SQL or Scope</font></span>:
                        Overrides other scopes you may have applied before calling  <span
                                style="font-family: &quot;Courier New&quot;;">accessible_by </span>- must use
                        as first scope
                </li>
                <li>
                    <font style="color: rgb(255, 38, 0); --inversion-type-color: simple;">Hash</font> (if
                        Raw SQL or Scope not defined) Constructs a<span
                                style="font-family: &quot;Courier New&quot;;"> LEFT JOIN </span>with them
                </li>
            </ul>
            <div><br/></div>
            <div>Ignores:</div>
            <ul>
                <li>
                    Block
                </li>
            </ul>
        </td>
    </tr>
    </tbody>
</table>

### Usage examples

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 77px;"/>
        <col style="width: 310px;"/>
        <col style="width: 250px;"/>
        <col style="width: 179px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div><br/></div>
        </td>
        <td style="width: 310px; padding: 8px; border: 1px solid;">
            <div>authorize!</div>
        </td>
        <td style="width: 250px; padding: 8px; border: 1px solid;">
            <div>can?</div>
        </td>
        <td style="width: 179px; padding: 8px; border: 1px solid;">
            <div>accessible_by</div>
        </td>
    </tr>
    <tr>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div>index</div>
        </td>
        <td style="width: 310px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>authorize! :index, Post</div>
                <div><br/></div>
                <div>@posts = Post.accessible_by(current_ability)</div>
            </div>
            <div/>
        </td>
        <td style="width: 250px; padding: 8px; border: 1px solid;">
            <div>Call on class (instance doesn’t make sense):</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>- if can? :index, Post</div>
                <div>  = link_to ’New post’, new_post_path</div>
            </div>
            <div/>
            <div/>
        </td>
        <td style="width: 179px; padding: 8px; border: 1px solid;">
            <div>Always <font face="Courier New">authorize!</font> first </div>
            <div><br/></div>
            <div>Always use as the first scope you apply</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>authorize! :index, Post</div>
                <div><br/></div>
                <div>@posts = Post.accessible_by(current_ability)</div>
            </div>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div>show</div>
        </td>
        <td style="width: 310px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>@post = Post.find(params[:id])</div>
                <div><br/></div>
                <div>authorize! :show, @post</div>
            </div>
            <div/>
        </td>
        <td style="width: 250px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>- @post.each do |post|<br/></div>
                <div>  - if can? :show, post</div>
            </div>
            <div/>
        </td>
        <td style="width: 179px; padding: 8px; border: 1px solid;">
            <div>Can be used for scoping parent resources</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>@subject = Subject.accessible_by(current_ability,:show)</div>
                <div><br/></div>
                <div>@article = @subject.articles.find(params[:id])</div>
                <div><br/></div>
                <div>authorize! :show, @article</div>
            </div>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div>create</div>
        </td>
        <td style="width: 310px; padding: 8px; border: 1px solid;">
            <div>Assign attributes before validating (but save after)</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>@post = Post.new(allowed_create_attributes)</div>
                <div><br/></div>
                <div>authorize! :create, @post</div>
            </div>
            <div/>
        </td>
        <td style="width: 250px; padding: 8px; border: 1px solid;">
            <div>Define new instance with known values:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>- if can?, :create, Post.new(subject: @subject, author: current_user)</div>
            </div>
            <div/>
        </td>
        <td style="width: 179px; padding: 8px; border: 1px solid;">
            <div>Not applicable</div>
        </td>
    </tr>
    <tr>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div>update</div>
        </td>
        <td style="width: 310px; padding: 8px; border: 1px solid;">
            <div>Check requirements for:</div>
            <div>* the current values of the resource using the hash<br/></div>
            <div>* new values using  the context<br/></div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>can :update, Post, user_id: user.id do |post, new_attributes|</div>
                <div>  # User is now allowed to update their own posts, but not change who the post is
                    by
                </div>
                <div>  post.user_id == user.id &amp;&amp; new_attributes[:user_id] == post.user_id</div>
                <div>end</div>
                <div><br/></div>
                <div>@post = Post.find(params[:id])</div>
                <div><br/></div>
                <div>authorize! :update, @post, allowed_update_params</div>
            </div>
            <div/>
        </td>
        <td style="width: 250px; padding: 8px; border: 1px solid;">
            <div>For generic update:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>- if can?, :update, @post</div>
                <div>  = link_to ‘Update’, edit_post_path(@post)</div>
            </div>
            <div/>
            <div><br/></div>
            <div>For sub-update:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>- if can?, :update, @post, { archived: true }</div>
                <div>  = link_to ‘Archive’, archive_post_path(@post)</div>
            </div>
            <div/>
            <div/>
        </td>
        <td style="width: 179px; padding: 8px; border: 1px solid;">
            <div>Useful for rendering a list of resources that the user can bulk update:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>@articles = Article.accessible_by(current_ability, :update)</div>
            </div>
            <div/>
        </td>
    </tr>
    <tr>
        <td style="width: 77px; padding: 8px; border: 1px solid;">
            <div>destroy</div>
        </td>
        <td style="width: 310px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>@post = Post.find(params[:id])</div>
                <div><br/></div>
                <div>authorize! :destroy, @post</div>
            </div>
            <div/>
        </td>
        <td style="width: 250px; padding: 8px; border: 1px solid;">
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>- if can?, :destroy, @post</div>
                <div>  = link_to ‘Delete’, post_path(@post), method: :delete</div>
            </div>
            <div/>
        </td>
        <td style="width: 179px; padding: 8px; border: 1px solid;">
            <div>Useful for rendering a list of resources that the user can bulk destroy:</div>
            <div><br/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>@articles = Article.accessible_by(current_ability, :destroy)</div>
            </div>
            <div/>
        </td>
    </tr>
    </tbody>
</table>

## Handling failures

For unauthorized requests, return `404` rather than `302` to avoid leaking information. However if you do want to render unauthorized:

Custom message to include in the exception thrown:

```ruby
authorize! :read, Article, :message => "Unable to read this article."
```

Raising exception manually:

```ruby
raise CanCan::AccessDenied.new("Not authorized!", :read, Article)
```

Rescuing:

```ruby
class ApplicationController < ActionController::Base
  rescue_from CanCan::AccessDenied do |exception|
    respond_to do |format|
      format.json { head :forbidden }
      format.html { redirect_to main_app.root_url, :alert => exception.message }
    end
  end
end

exception.action # => :read
exception.subject # => Article
```

## Troubleshooting

### Logging details of AccessDenied exceptions

```ruby
# in ApplicationController
rescue_from CanCan::AccessDenied do |exception|
  if Rails.env.development?
    matching_rules = current_ability.send(:relevant_rules_for_match, exception.action, exception.subject)

    if matching_rules.any?
      describe_rules = matching_rules.map {|rule| { conditions: rule.conditions, block: rule.instance_variable_get(:@block) } }
     
      Rails.logger.debug "CanCanCan::AccessDenied: User has ability to #{exception.action} " \
                         "#{exception.subject.inspect}, but failed to satisfy conditions: " \
                         "#{describe_rules.to_sentence(two_words_connector: ' or ', last_word_connector: ' or ')}"
    else
      Rails.logger.debug "CanCanCan::AccessDenied: User does not have ability to #{exception.action} " \
                         "#{exception.subject.inspect}"
    end
  end
end
```

### Order code is executed

* Request is made and assigned to a controller
* Controller instance calls current_ability method
* `current_ability` instantiates an instance of `CanCan::Ability` class (so your `ability.rb` file’s initialize method is called)

Case: For `authorize!` or `can?`

* Controller makes first call to `authorize!` or `can?` (which calls `can?` on your `current_ability` instance)
* Matching rules are found based on the subject’s class and action
* If the subject is a class, this is where the logic stops (and we only check for a matching action)
* If the subject is an instance
    * If a block is defined, its evaluated, and logic ends
    * If a hash is defined, its values are matched against the subject’s attributes

Case: For `accessible_by`

* Controller calls `model_adaptor` on your `current_ability` and other methods to find matching rules based on the subject’s class and action
* Returns a `ActiveRecord::Relation` for the subject model class with the raw SQL or scope if one has bee defined, otherwise a `LEFT JOIN` with the attributes from the hash

<table style="border-collapse: collapse; min-width: 100%;">
    <colgroup>
        <col style="width: 107px;"/>
        <col style="width: 375px;"/>
        <col style="width: 333px;"/>
    </colgroup>
    <tbody>
    <tr>
        <td rowspan="1" style="width: 107px; padding: 8px; border: 1px solid;">
            <div>Checking</div>
        </td>
        <td rowspan="1" style="width: 375px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">authorize!</span> or <span
                    style="font-family: &quot;Courier New&quot;;">can?</span></div>
        </td>
        <td rowspan="1" style="width: 333px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Courier New&quot;;">accessible_by</span></div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 107px; padding: 8px; border: 1px solid;">
            <div>Current user and/or ability.rb logic</div>
        </td>
        <td colspan="2" rowspan="1" style="width: 708px; padding: 8px; border: 1px solid;">
            <div>Place logging statements in your <span style="font-family: &quot;Courier New&quot;;">ability.rb</span>
                file
            </div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 107px; padding: 8px; border: 1px solid;">
            <div>Action and Subject</div>
        </td>
        <td rowspan="1" style="width: 375px; padding: 8px; border: 1px solid;">
            <div>Rescue failed <span style="font-family: &quot;Courier New&quot;;">authorize!</span> in
                controller: 
            </div>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">rescue_from CanCan::AccessDenied </span>do
                    |exception|
                </div>
                <div>  p exception.subject</div>
                <div>  p exception.action</div>
                <div>end</div>
            </div>
            <div><br clear="none"/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Rails console:</span></div>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>user = User.create!</div>
                <div>ability = <span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">Ability.new(user)</span>
                </div>
                <div>ability.<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">can?</span>(:destroy,
                    Project.new(user: user)<span
                            style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</span></div>
            </div>
            <div><br clear="none"/></div>
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">See if there is a matching rule for class and action:</span>
            </div>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div><span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">current_ability.send(:relevant_rules_for_match, action, class_or_instance)</span>
                </div>
            </div>
            <div><br clear="none"/></div>
            <div><span
                    style="font-family: &quot;Helvetica Neue&quot;;">See if the rule’s conditions match:</span>
            </div>
            <ul>
                <li>
                    <span style="-en-paragraph:true;">If not, use techniques below depending on how you’re defining your matching rule </span>
                </li>
            </ul>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>current_ability.send(:relevant_rules_for_match, action, class_or_instance).first<span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.matches_conditions?(action, subject, {})</span>
                </div>
            </div>
            <div><br clear="none"/></div>
        </td>
        <td rowspan="1" style="width: 333px; padding: 8px; border: 1px solid;">
            <div>Make sure:</div>
            <ul>
                <li>
                    Check class you’re calling accessible_by on and the arguments you’re passing it
                </li>
            </ul>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div><span
                        style="font-size: 12px; font-family: Monaco; color: rgb(255, 38, 0); --inversion-type-color: simple;">current_ability.can?(:index, ModelClass)</span>
                </div>
            </div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 107px; padding: 8px; border: 1px solid;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(20, 20, 20);">Raw SQL or scope</span>
            </div>
        </td>
        <td rowspan="1" style="width: 375px; padding: 8px; border: 1px solid;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(110, 110, 110);">Not applicable</span>
            </div>
            <div><br clear="none"/></div>
        </td>
        <td rowspan="1" style="width: 333px; padding: 8px; border: 1px solid;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(20, 20, 20); font-family: &quot;Helvetica Neue&quot;;">See SQL (works for hashes too):</span>
            </div>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>Project.accessible_by(ability)<span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.to_sql</span></div>
            </div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 107px; padding: 8px; border: 1px solid;">
            <div>Hash</div>
        </td>
        <td rowspan="1" style="width: 375px; padding: 8px; border: 1px solid;">
            <div>See the hash that’s used to match the instance’s values:</div>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>current_ability.model_adapter(exception.subject, exception.action)<span
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.conditions</span>
                </div>
            </div>
            <div><br clear="none"/></div>
        </td>
        <td rowspan="1" style="width: 333px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">See hash passed to where:</span></div>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>current_ability<span style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.model_adapter(TargetClass, :read).conditions</span>
                </div>
            </div>
            <div><br clear="none"/></div>
            <div>Test query by performing the same <font face="Courier New">LEFT JOIN</font> CanCanCan does:
            </div>
            <div><br clear="none"/></div>
            <div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-top-left-radius: 4px; border-top-right-radius: 4px; border-bottom-right-radius: 4px; border-bottom-left-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;">
                <div>User.where(id: current_user.id)<font
                        style="color: rgb(255, 38, 0); --inversion-type-color: simple;">.joins(</font>current_ability.model_adapter(TargetClass,
                    :read).conditions<font
                            style="color: rgb(255, 38, 0); --inversion-type-color: simple;">)</font></div>
            </div>
        </td>
    </tr>
    <tr>
        <td rowspan="1" style="width: 107px; padding: 8px; border: 1px solid;">
            <div>Block</div>
        </td>
        <td rowspan="1" style="width: 375px; padding: 8px; border: 1px solid;">
            <div><span style="font-family: &quot;Helvetica Neue&quot;;">Place logging or debugging statement in the block and check the result that’s returned</span>
            </div>
        </td>
        <td rowspan="1" style="width: 333px; padding: 8px; border: 1px solid;">
            <div><span
                    style="--inversion-type-color:  simple; color: rgb(110, 110, 110);">Not applicable</span>
            </div>
        </td>
    </tr>
    </tbody>
</table>


### Common exceptions

```ruby
undefined method `klass' for nil:NilClass
```                                      

Occurs when you've defined an association name incorrectly in a condition hash.
