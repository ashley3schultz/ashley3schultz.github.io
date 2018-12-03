---
layout: post
title:      "Has And Blongs To Many - Same Model"
date:       2018-12-03 06:57:04 +0000
permalink:  has_and_blongs_to_many_-_same_model
---

I've been wanting to test a model relationship for a long time where a user can have many other users and a user can belong to a another user. This is a test application I will be building on. In this scenerio I am pairing users up so if one relationship is made it will go in two directions. This would work one direction if you remove the "pairs" bit of code in which case you can find the member by calling @member.membes.

## The Code 

### Model 

**member.rb**
```
class Member < ApplicationRecord
    has_and_belongs_to_many(:members,
    :join_table => "member_connections",
    :foreign_key => "member_a_id",
    :association_foreign_key => "member_b_id")

    def self.pairs(id)
        pairs = []
        MemberConnection.where(member_a_id: id).each do |m| 
            mem = Member.find(m.member_b_id)
            pairs << mem 
        end
        MemberConnection.where(member_b_id: id).each do |m| 
            mem = Member.find(m.member_a_id)
            pairs << mem
        end 
        pairs
    end 
end
```

**member_connections.rb**
```
class MemberConnection < ApplicationRecord
    belongs_to :member_a, :class_name => :Member
    belongs_to :member_b, :class_name => :Member
end
```

### Controller

**members_controller.rb**
```
class MembersController < ApplicationController

    def index 
        @members = Member.all
    end

    def show 
        @member = Member.find_by(id: params[:id])
        @pairs = Member.pairs(@member.id)
    end 

end
```

### View 

**index.erb**
```
<div>
<% @members.each do |member| %>
    <p><%= member.name %></p>
<% end %>
</div>
```

**show.erb**
```
<div>
<h2><%= @member.name %></h2>
<p><%= @pairs.first.name %></p>
</div>
```



