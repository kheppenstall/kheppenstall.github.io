---
layout: post
title:  "Mapping Ruby to ActiveRecord"
date:   2016-12-05 11:16:29 -0500
categories: ruby activerecord
---

## Introduction

In any web application, using Ruby to parse data after querying your database is extremely slow. I learned that the hard way. That is not what Ruby was built for. Instead, prefer ActiveRecord methods after your queries. But that means learning a whole new set of methods. This document is to help you learn the ActiveRecord equivalent of Ruby enumerables. Ruby enumerables to ActiveRecord query methods and calculations is not a 1 to 1 mapping. These should be considered as examples to help understand ActiveRecord methods and figure out the best methods to use yourself.

[Ruby Enumerables Documentation](https://ruby-doc.org/core-2.2.3/Enumerable.html)

[ActiveRecord Documentation](http://api.rubyonrails.org/classes/ActiveRecord/QueryMethods.html)

## Example Context
Let's consider students, classes, and teachers. Students can be enrolled in many classes. Teachers can teach many classes. Classes can have many students but only one teacher.


And here are our example relationships:

* Many students to many classes
* 1 teacher to many classes

#### Models
```ruby
class Teacher 
  has_many :classes
  has_many :students, through: :classes
end

class Student 
  has_many :classes
  has_many :students, through: :classes
end

class Class 
  belongs_to :teacher
  belongs_to :student 
end
```

And here are the attributes of each model.

Students have a `first_name`, `last_name`, and `id`.

Teachers have a `first_name`, `last_name`, and `id`.

Classes have a `name`, `enrollment` (integer), `id`.


## Example Maps

* #### `find_all` / `select` (Ruby) to `where` (ActiveRecord)

  ```ruby
  # Find all students with the first name John

  # Ruby
  Student.all.find_all {|student| student.first_name == 'John'}  

  #ActiveRecord
  Student.where(name: 'John')
  ```

* #### `sort_by` (Ruby) to `order` (ActiveRecord) with one argument

  ```ruby
  # Sort students by last name

  # Ruby
  Student.all.sort_by {|student| student.last_name}

  # ActiveRecord
  Student.order(:last_name)
  ```

* #### `map` (Ruby) to `pluck` (ActiveRecord)

  ```ruby
  # List of first names of all students

  #Ruby
  Student.all.map {|student| student.first_name}

  # ActiveRecord
  Student.pluck(:first_name)
  ```

* #### `reduce` (Ruby) to `sum` (ActiveRecord)

  ```ruby
  # The sum of enrollments over all classes.

  # Ruby
  Class.all.reduce(0) {|sum, class| sum += class.enrollment}

  #ActiveRecord
  Class.sum(:enrollment)
  ```

* #### `map` and `compact` (Ruby) to `joins` and `distinct`

  ```ruby
  # Find all students enrolled in one or more classes

  #Ruby
  Student.all.map {|student| student if student.classes.present?}.compact
  
  #ActiveRecord
  Student.joins(:classes).includes(:classes).distinct
  ```

* #### `max_by` (Ruby) `maximum` (ActiveRecord)
  ```ruby
  # Find the class with the most students

  # Ruby
  Class.all.max_by {|class| class.enrollment}

  # ActiveRecord
  Class.maximum(:enrollment)
  ```


* #### `group_by` (Ruby) to `joins` and `group` (ActiveRecord)
  ```ruby
  # Return an array with teacher last name as key and number of students the teacher has as value 

  #Ruby
  Teacher.all.reduce(Hash.new(0)) do |name_to_students, teacher|
    name_to_students[teacher.name] = teacher.students.count
  end

  # ActiveRecord
  Teacher.joins(:students).group(:last_name).count(:id)
  ```

* #### `sort_by` (Ruby) to `order` (ActiveRecord) with multiple arguments

  ```ruby
  # Sort students by last name, then first name

  #Ruby
  Student.all.sort_by {|student| [student.last_name, student.first_name]}

  # ActiveRecord
  Student.order(:last_name, :first_name)
  ```

* #### `map` (Ruby) to `where` (ActiveRecord) when finding all execept

  ```ruby
  # Return all students not named Joe, John, or Maggie
  first_names = ['Joe', 'John', 'Maggie']

  #Ruby
  Student.all.map {|student| student unless first_names.include?(student.first_name)}.compact

  # ActiveRecord
  Student.where.not(first_name: first_names)
  ```