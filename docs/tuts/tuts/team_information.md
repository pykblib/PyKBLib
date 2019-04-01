Interacting with Teams
======================

Retrieving a Team List
----------------------
To retrieve a list of the teams to which the active user belongs, simply use the `Keybase.teams` attribute. You can use this, for example, to print out the list:

```
print("Team Memberships:")
for team_name in KB.teams:
    print(f" - {team_name}")
```

Updating the Team List
----------------------
The `Keybase.teams` list is populated when the `Keybase` class is instantiated. However, it does not actively track changes in the active user's memberships. The `update_team_list` function is used to update this list. To update the list of team memberships, simply do the following:

```
KB.update_team_list()
```

Note that calling this function will incur a slight delay, as the library must request the data from the Keybase application, which can take some time.

Creating a Team Instance
------------------------
To interact with a team directly, you'll want to create an instance of the `Team` class. This can be done by using the `Keybase.team` function:

```
TEAM = KB.team("team_name")
```

The above code will create a `Team` instance connected to the team called "team_name". (You'll want to change "team_name" to the name of the team with which you wish to interact.) It is possible to access team information without assigning the output from the `Keybase.team` function to a variable, like so:

```
team_name = "team_name"
member_count = KB.team(team_name).member_count
print(f"The '{team_name}' team has {member_count} members.")
```

However, if you need to interact with the team more than once, it is best to store the `Team` instance in a variable, as in the first example. Each time you instantiate a `Team`, there will be a slight delay as the script communicates with the Keybase app, so it's faster to store the `Team` object in a variable rather than creating it repeatedly.

In the following tutorials, we'll refer to the `TEAM` variable when showing how to interact with `Team` objects.

Accessing Team Information
--------------------------
Most information about a team is accessible through the `Team` class' attributes. So far, the following attributes have been implemented:

* `Team.name` (*str*) The name of the team.
* `Team.role` (*str*) The team role assigned to the current user.
* `Team.member_count` (*int*) The number of members in the team.
* `Team.members` (*list*) A list of the team's members.
* `Team.members_by_role` (*namedtuple*) A namedtuple containing lists of members sorted into their respective roles:
    * `Team.members_by_role.owner` (*list*) A list of all the owners of the team.
    * `Team.members_by_role.admin` (*list*) A list of all the admins of the team.
    * `Team.members_by_role.writer` (*list*) A list of all the writers in the team.
    * `Team.members_by_role.reader` (*list*) A list of all the readers in the team.
* `Team.deleted` (*list*) A list of members who have deleted their accounts.

If you wanted to print out a summary of this information, you could do the following:

```
print(f"Information for team {TEAM.name}:")
print(f"- role: {TEAM.role}")
print(f"- member_count: {TEAM.member_count}")
print("- team members:")
for member in TEAM.members:
    print(f"  - {member}")
print("- owners:")
for member in TEAM.members_by_role.owner:
    print(f"  - {member}")
print("- admins:")
for member in TEAM.members_by_role.admin:
    print(f"  - {member}")
print("- writers:")
for member in TEAM.members_by_role.writer:
    print(f"  - {member}")
print("- readers:")
for member in TEAM.members_by_role.reader:
    print(f"  - {member}")
print("- deleted accounts:")
for member in TEAM.deleted:
    print(f"  - {member}")
```

The membership and role information is populated at the creation of the `Team` instance. In order to update the team membership information, simply use the `Team.update` function:

```
# Update the team's membership and role information.
TEAM.update()
```

Note that this function incurs a slight delay, due to the fact that it must retrieve fresh information from Keybase.

Adding and Removing Members
---------------------------
To add or remove members from a team, you must be an admin or owner of that team. Adding and removing team members can be done with the `Team.add_member` and `Team.remove_member` functions, respectively. For example:

```
# Add the 'pykblib' user to the TEAM.
TEAM.add_member("pykblib")
# Remove the 'pykblib' user from the TEAM.
TEAM.remove_member("pykblib")
```

The `Team.add_member` function assigns the `reader` role to the new member by default. In order to assign a different role, simply specify it as an additional parameter to the `Team.add_member` function:

```
# Add the 'pykblib' user to the TEAM as a writer.
TEAM.add_member("pykblib", "writer")
```

The role assigned must be one of the following:

* `reader` - Can read and write to chat, and can read the team's KBFS resources, including Git repositories.
* `writer` - Can write to the team's KBFS resources, including Git repositories.
* `admin` - Can add or remove admins, readers, and writers to the team, and can establish new sub-teams. They can also deactivate sub-teams.
* `owner` - Can do anything admins can do, but also has the ability to delete the team.

For more information, see [the official Keybase team documentation](https://keybase.io/docs/teams/design). *Note: While each of these roles may have the specified abilities, many of these abilities have not yet been implemented in PyKBLib.*