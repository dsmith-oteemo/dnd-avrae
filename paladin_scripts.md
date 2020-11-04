#####
#Corrections for omisions and errors on char sheet

#light spell does not import properly with parens in the title "light (from race)"

!sb add light

#oath spells not included on char sheet

!sb add sanctuary
!sb add sleep

#####
#Aasimar power setup

!test <drac2>
if not character().cc_exists("Healing Hands"):
  character().create_cc("Healing Hands",0,1,"long","bubble")
if not character().cc_exists("Radiant Soul"):
  character().create_cc("Radiant Soul",0,1,"long","bubble")
</drac2>

#####
#Healing hands

!alias healinghands embed
<drac2>
args=argparse(&ARGS&)
targets=args.get('t')
target = ""
failure = False
if ((combat()) and (len(targets > 0)) and (character().get_cc("Healing Hands") > 0)):
  target = combat().getcombatant(targets[0]).name
  combat().getcombatatnt(target).set_hp(+level)
  character().set_cc("Healing Hands",(character().get_cc("Healing Hands") - 1 ) )
else:
  failure = True
</drac2>
-title "{{ <name> used Healing hands on <target> }}"

#Radiant Soul

