########################################################################################################################################

###Author: Dave Smith 2020

###This Script below needs to be a server snippet; everyone who consumes Bardic inspiration will need it.



###Use Bardic Inspiration die -- COMBAT ONLY
###Usage: !i a "Light Crossbow" -t "Monster" bi
###Usage: !check perception bi

!serversnippet bi -b {{ bid = combat().me.get_effect("Bardic Inspiration") }} {{ bid.name.split('_')[0] if bid else 0 }} {{  combat().me.remove_effect("Bardic Inspiration") }}

### IMPORTANT ^^^ Make sure this server snippet abover gets on the server. Otherwise party members wont be able to use bardic inspiration dice

########################################################################################################################################

###Run the bard script below once more when you hit level 5. it will fix up your BI charges to recharge on
###a short rest. Still a lot of stuff to codify, but not urgent for a while since I am only level 3.


########################################################################################################################################

###Establish Bard Inspiration Self Counter

!test <drac2>
if character().cc_exists("Bardic Inspiration"):
  character().delete_cc("Bardic Inspiration")
if (level < 5 ):
  character().create_cc("Bardic Inspiration",0,charismaMod,"long","bubble")
else:
  character().create_cc("Bardic Inspiration",0,charismaMod,"short","bubble")
</drac2>


###Establish Goblin Fury of the Small Counter

!test <drac2>
if not character().cc_exists("Fury of the Small"):
  character().create_cc("Fury of the Small",0,1,"short","bubble")
</drac2>


###Use fury of the small -- COMBAT ONLY
###Usage: !i a "Light Crossbow" -t "Monster" fury

!serversnippet fury -d "{{level if character().get_cc("Fury of the Small") > 0 else 0}} [Fury]" {{ character().set_cc("Fury of the Small",0) }}


###Use bardic inspiration Die to inspire someone --- COMBAT ONLY
###Usage: !inspire "Player"

!alias inspire embed 
<drac2>
if (character().get_cc("Bardic Inspiration") > 0 ):
  character().set_cc("Bardic Inspiration", character().get_cc("Bardic Inspiration") - 1)
  args=argparse(&ARGS&)
  targets=args.get('t')
  target=combat().get_combatant(targets[0]).name
  if (level >= 15):
    inspDice = "1d12"
  elif (level >= 10):
    inspDice = "1d10"
  elif (level >= 5):
    inspDice = "1d8"
  else:
    inspDice = "1d6"
  combat().get_combatant(target).add_effect(f'{inspDice}'"_Bardic Inspiration","",100,"","","","add " f'{inspDice}' " to save, attack or check")
  failure = False
else:
  failure = True
</drac2>
-title "{{f'{character().name} {"Inspires" if not failure else "fails to inspire"} {target}!' }}" 
-desc "You can inspire others through stirring words or music. To do so, you use a bonus action on your turn to choose one creature other than yourself within 60 feet of you who can hear you. That creature gains one Bardic Inspiration die, a d6. Once within the next 10 minutes, the creature can roll the die and add the number rolled to one ability check, attack roll, or saving throw it makes. The creature can wait until after it rolls the d20 before deciding to use the Bardic Inspiration die, but must decide before the DM says whether the roll succeeds or fails. Once the Bardic Inspiration die is rolled, it is lost. A creature can have only one Bardic Inspiration die at a time. You can use this feature a number of times equal to your Charisma modifier (a minimum of once). You regain any expended uses when you finish a long rest. Your Bardic Inspiration die changes when you reach certain levels in this class. The die becomes a d8 at 5th level, a d10 at 10th level, and a d12 at 15th level." 
-f "Effects: {{ "Add" f'{inspDice}' "to any ability check, attack, or saving throw." if not failure else "None"}}"
-f "Target: {{ f'{target}'}}"
-f "{{"Bardic Inspiration"}}|{{'◉'*get_cc("Bardic Inspiration") + '〇'*(get_cc_max("Bardic Inspiration")-get_cc("Bardic Inspiration"))}}"
-footer "Source PHB"



###Use Mantle of Inspiration -- COMBAT ONLY
###Usage: mantle "Player1" "Player2" "Player3" "Player4"

!alias mantle embed
<drac2>
if (character().get_cc("Bardic Inspiration") > 0 ):
  if (level <= 4):
    tmpHP = 5
  elif (level <= 9):
    tmpHP = 8
  elif (level <= 14):
    tmpHP = 11
  else:
    tmpHP = 14
  args=argparse(&ARGS&)
  targets=args.get('t')
  names = []
  if (len(targets)<=charismaMod):
    character().set_cc("Bardic Inspiration", character().get_cc("Bardic Inspiration") - 1)
    for target in targets:
      names.append(combat().get_combatant(target).name)
      combat().get_combatant(target).set_temp_hp(tmpHP)
    failure = False
  else:
    failure = True
else:
  failure = True
</drac2>
-title "{{f'{character().name} {"casts Mantle of Inspiration" if not failure else "fails to cast Mantle of Inspiration"}!' }}" 
-desc "When you join the College of Glamour at 3rd level, you gain the ability to weave a song of fey magic that imbues your allies with vigor and speed. As a bonus action, you can expend one use of your Bardic Inspiration to grant yourself a wondrous appearance. When you do so, choose a number of creatures you can see and who can see you within 60 feet of you, up to a number equal to your Charisma modifier (minimum of one). Each of them gains 5 temporary hit points. When a creature gains these temporary hit points, it can immediately use its reaction to move up to its speed, without provoking opportunity attacks. The number of temporary hit points increases when you reach certain levels in this class, increasing to 8 at 5th level, 11 at 10th level, and 14 at 15th level." 
-f "Effects:"
-f "Targets:{{ f'{", ".join(names) if not failure else "No targets effected"}' }}"
-f "Temporary HP per target = {{ f'{tmpHP if not failure else "0"}' }}"
-f "Targets may use a reaction to move up to their movement speed without provoking opportunity attacks." 
-f "{{"Bardic Inspiration"}}|{{'◉'*get_cc("Bardic Inspiration") + '〇'*(get_cc_max("Bardic Inspiration")-get_cc("Bardic Inspiration"))}}"
-footer "Source XGE"


###Enthralling performance -- no targeting since this one will be used outside combat.
###Usage: !enthrall

!alias enthrall embed
<drac2>
if (character().get_cc("Bardic Inspiration") > 0 ):
  args=argparse(&ARGS&)
  character().set_cc("Bardic Inspiration", character().get_cc("Bardic Inspiration") - 1)
  failure = False
else:
  failure = True
</drac2>
-title "{{f'{character().name} {"casts Enthralling Performance" if not failure else "fails to cast Enthralling Performance"}!' }}" 
-desc "Starting at 3rd level, you can charge your performance with seductive, fey magic. If you perform for at least 1 minute, you can attempt to inspire wonder in your audience by singing, reciting a poem, or dancing. At the end of the performance, choose a number of humanoids within 60 feet of you who watched and listened to all of it, up to a number equal to your Charisma modifier (minimum of one). Each target must succeed on a Wisdom saving throw against your spell save DC or be charmed by you. While charmed in this way, the target idolizes you, it speaks glowingly of you to anyone who speaks to it, and it hinders anyone who opposes you, avoiding violence unless it was already inclined to fight on your behalf. This effect ends on a target after 1 hour, if it takes any damage, if you attack it, or if it witnesses you attacking or damaging any of its allies. If a target succeeds on its saving throw, the target has no hint that you tried to charm it. Once you use this feature, you can’t use it again until you finish a short or long rest." 
-f "{{"Bardic Inspiration"}}|{{'◉'*get_cc("Bardic Inspiration") + '〇'*(get_cc_max("Bardic Inspiration")-get_cc("Bardic Inspiration"))}}"
-footer "Source XGE"

###Song of Rest -- again no targeting, would be nice to be able to target characters directly, but so far I am only able to do it in combat.
###Usage: !song

!alias song embed
<drac2>
if (level >= 17):
  healDice = "1d12"
elif (level >= 13):
  healDice = "1d10"
elif (level >= 9):
  healDice = "1d8"
else:
  healDice = "1d6"
</drac2>
-title "{{character().name}} casts Song of Rest"
-desc "Beginning at 2nd level, you can use soothing music or oration to help revitalize your wounded allies during a short rest. If you or any friendly creatures who can hear your performance regain hit points by spending Hit Dice at the end of the short rest, each of those creatures regains an extra 1d6 hit points. The extra hit points increase when you reach certain levels in this class: to 1d8 at 9th level, to 1d10 at 13th level, and to 1d12 at 17th level." 
-f "Effect: All players add {{ healDice }} to HP when spending a hit die to recover during a short rest."
-footer "Source PHB"