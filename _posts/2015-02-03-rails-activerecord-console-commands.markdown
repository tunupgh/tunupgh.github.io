---
layout: post
title: Rails ActiveRecord Console Commands
date: 2015-02-03 20:34
comments: true
categories:
- coding
- rails
- activerecord
---
It is possible to interact with the database of a Ruby on Rails project from the console. To enter the console execute this command from the command line within your project:

`rails console`

In our examples below we will be using the following databases. The relationship is that a `user` has many `pets`.

<table class="table">
 <caption>users</caption>
 <th>id</th>
 <th>username</th>
 <th>password</th>
 <tr>
  <td>1</td>
  <td>Alex</td>
  <td>al23lk@#$AFfFj23rhf0a98eraljkwqer</td>
 </tr>
 <tr>
  <td>2</td>
  <td>Benny</td>
  <td>2llLKJ#3j3#KJ#L7ouOuoI#</td>
 </tr>
 <tr>
  <td>7</td>
  <td>Carlton</td>
  <td>#LKJ#LkjJ9f9wf0j</td>
 </tr>
</table>

<table class="table">
 <caption>pets</caption>
 <th>id</th>
 <th>user_id</th>
 <th>pet_name</th>
 <tr>
  <td>1</td>
  <td>2</td>
  <td>Scruffy</td>
 </tr>
 <tr>
  <td>2</td>
  <td>2</td>
  <td>Buddy</td>
 </tr>
 <tr>
  <td>3</td>
  <td>3</td>
  <td>Buttons</td>
 </tr>
</table>

### Viewing a record

To view the first **user**:<br>
```
2.1.1 :003 > User.first
 => #<User id: 1, username: "Alex", password: "al23lk@#$AFfFj23rhf0a98eraljkwqer"> 
```

To view the 3rd **user**:<br>
```
2.1.1 :003 > User.find(3)
 => #<User id: 3, username: "Benny", password: "2llLKJ#3j3#KJ#L7ouOuoI#"> 
```

To find the **pet** with the name "Buttons":<br>
```
2.1.1 :009 > Pet.find_by_pet_name('Buttons')
 => #<Pet id: 3, user_id: 3, pet_name: "Buttons"> 
```

Using the **where** command: 

```
2.1.1 :028 > User.where(username: "Carlton")
 => #<ActiveRecord::Relation [#<User id: 3, username: "Carlton", password: "#LKJ#LkjJ9f9wf0j">]> 
```

### Using variables

In order to manipulate the data in the database it's best to use variables. They are easy to use and very helpful.

To assign the first user to variable **a** and then display the contents of a:

```
2.1.1 :012 > a = User.first
 => #<User id: 1, username: "Alex", password: "al23lk@#$AFfFj23rhf0a98eraljkwqer"> 
2.1.1 :013 > a
 => #<User id: 1, username: "Alex", password: "al23lk@#$AFfFj23rhf0a98eraljkwqer"> 
```

To change the username for **a**:

```
2.1.1 :016 > a.username = "Alexander"
 => "Alexander" 
2.1.1 :017 > a
 => #<User id: 1, username: "Alexander", password: "al23lk@#$AFfFj23rhf0a98eraljkwqer"> 
```

To save the new changes to the database:

```
2.1.1 :018 > a.save
 => true 
```

If the result says 'true' it means the data was saved to the database. If it comes back 'false' it means there's a problem and the data wasn't saved. The validations rules still apply that are in the model. Also if you have before save filters in the model that may cause the save to fail.

### Create a new record

There are two ways to create a new record. Using the 'save' option or 'create' option.

Using the **save** method:

```
2.1.1 :019 > a = User.new
 => #<User id: nil, username: nil, password: nil, created_at: nil, updated_at: nil> 
2.1.1 :020 > a.username = "Darcy"
 => "Darcy" 
2.1.1 :021 > a.save
 => true 
```
Using the **create** method:

```
2.1.1 :011 > User.create(:username => "Darcy")
```




### Further reading

[http://guides.rubyonrails.org/active_record_querying.html](http://guides.rubyonrails.org/active_record_querying.html)