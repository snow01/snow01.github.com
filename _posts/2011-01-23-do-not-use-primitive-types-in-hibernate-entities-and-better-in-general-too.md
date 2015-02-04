---
layout: post
title: Do not use primitive types in hibernate entities and better in general too
date: 2011-01-23 01:16
author: shailendra
comments: true
categories: [Hibernate, ORM, Primitive Types, Tech]
---
Most programmers tend to chose primitive types wherever applicable - in most applications one would not see issues with such an approach, but such practices can lead to some hard to find bugs - specially when you are using technologies like hibernate.

Let's take an example from an application built in Hibernate, Seam and JSF --

You have an entity called MenuItem. MenuItem has name and Action associated with the item (on item selection).

public class MenuItem
{
private long id;
private String itemName;
private Action action;
}

public class Action
{
private long id;
private String actionName;
}

You have a MenuItemEdit.xhtml page that allows creation of MenuItem and there is 'Select' button to select the associated action from list of available actions in ActionList.xhtml.

MenuItemEdit.xhtml page is backed by MenuItemHome, a PersitenceController implementation. Most typical implementation of MenuItemHome would have code to load an entity from the Persistence Context, if id for the entity is defined - while create a new instance of entity (a transient entity) if id is not defined.

On press of Select button, id of the current menu item need to be passed to ActionList page so selected item can be assigned to the right entity. For an existing item, id would be a valid long value. <strong>While for newly created item, id would not exist. </strong>With id being long value, 'id would not exist' would mean it has zero value - the default value of long. On postback from ActionList page, selected item would be attempted for assignment to a zero id. Persistence controller would try But in Persistence context, there is no entity with 0 id -- so you are going to get error or a bug.

But if you would have kept the id as Long type -- then 'id would not exist' would have simply meant a null value. Your home class would always know that it should not try load the entity from persistence context un-necessarily.

In general too, primitive types are dangerous -- they do not have notion of undefined value. Application developers coming from procedural world (like C), they know only one mechanism to define an undefined value -- a assumed value (like zero, max long value or min long value). This works till they are taking care of this assumption everywhere the value is getting accessed. But when they start integrating with external system - those systems does not know these assumed values and bugs start creeping. Over that, remembering all such assumed values in an application is a huge maintenance cost.

So, all developers please do not use primitive types as far as possible, unless you are writing software for a memory constraint device and doing lot of bit packing. But even in that case I would suggest them to use primitive wrappers. One such implementation is freely available for Java developers from Apache Commons: http://commons.apache.org/primitives/

Thanks !
