# README

This explains the difference between validating the presence of a model vs an id.

With a model the object just has to be present to make the record valid. It does not have to be persisted yet. With the id, the validation ensures that the object is persisted before the assotiation is valid.

`validates_presence_of :company_id` and `validates_presence_of :user`

```
irb(main):001:0> user = User.new
=> #<User id: nil, name: nil, created_at: nil, updated_at: nil, company_id: nil>

irb(main):002:0> profile = Profile.new
=> #<Profile id: nil, user_id: nil, time_zone: nil, created_at: nil, updated_at: nil>

irb(main):003:0> company = Company.new
=> #<Company id: nil, name: nil, created_at: nil, updated_at: nil>

irb(main):004:0> profile.user = user
=> #<User id: nil, name: nil, created_at: nil, updated_at: nil, company_id: nil>

irb(main):005:0> user.company = company
=> #<Company id: nil, name: nil, created_at: nil, updated_at: nil>

irb(main):006:0> user.valid?
=> false

irb(main):007:0> profile.valid?
=> true

irb(main):008:0> company.save
   (0.1ms)  begin transaction
  SQL (1.1ms)  INSERT INTO "companies" ("created_at", "updated_at") VALUES (?, ?)  [["created_at", "2015-10-07 13:23:40.041877"], ["updated_at", "2015-10-07 13:23:40.041877"]]
   (0.6ms)  commit transaction
=> true

irb(main):009:0> user.valid?
=> false

irb(main):010:0> user.company = company
=> #<Company id: 1, name: nil, created_at: "2015-10-07 13:23:40", updated_at: "2015-10-07 13:23:40">

irb(main):011:0> user.valid?
=> true

irb(main):012:0> user.save
   (0.1ms)  begin transaction
  SQL (1.6ms)  INSERT INTO "users" ("company_id", "created_at", "updated_at") VALUES (?, ?, ?)  [["company_id", 1], ["created_at", "2015-10-07 13:27:30.753895"], ["updated_at", "2015-10-07 13:27:30.753895"]]
   (0.6ms)  commit transaction
=> true

irb(main):013:0> Company.destroy_all
  Company Load (0.1ms)  SELECT "companies".* FROM "companies"
   (0.1ms)  begin transaction
  SQL (0.2ms)  DELETE FROM "companies" WHERE "companies"."id" = ?  [["id", 1]]
   (1.3ms)  commit transaction
   (0.0ms)  begin transaction
=> [#<Company id: 1, name: nil, created_at: "2015-10-07 13:23:40", updated_at: "2015-10-07 13:23:40">]

irb(main):014:0> user.valid?
=> true

irb(main):015:0> user = User.first
  User Load (0.2ms)  SELECT  "users".* FROM "users"  ORDER BY "users"."id" ASC LIMIT 1
=> #<User id: 1, name: nil, created_at: "2015-10-07 13:27:30", updated_at: "2015-10-07 13:27:30", company_id: 1>

irb(main):016:0> user.valid?
=> true

irb(main):017:0> user.company
  Company Load (0.1ms)  SELECT  "companies".* FROM "companies" WHERE "companies"."id" = ? LIMIT 1  [["id", 1]]
=> nil

irb(main):018:0> User.destroy_all
  User Load (0.1ms)  SELECT "users".* FROM "users"
   (0.0ms)  begin transaction
  SQL (0.3ms)  DELETE FROM "users" WHERE "users"."id" = ?  [["id", 1]]
   (1.4ms)  commit transaction
=> [#<User id: 1, name: nil, created_at: "2015-10-07 13:27:30", updated_at: "2015-10-07 13:27:30", company_id: 1>]

irb(main):019:0> profile.valid?
=> true

irb(main):020:0> profile = Profile.first
  Profile Load (0.2ms)  SELECT  "profiles".* FROM "profiles"  ORDER BY "profiles"."id" ASC LIMIT 1
=> #<Profile id: 1, user_id: nil, time_zone: nil, created_at: "2015-10-07 13:26:10", updated_at: "2015-10-07 13:26:10">

irb(main):021:0> profile.valid?
=> false
```
