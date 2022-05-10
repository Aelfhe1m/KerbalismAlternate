# KerbalismAlternate
An alternative way of configuring Kerbalism

Very early stages of development - not useable yet

General process:

  * Each feature is enable by a KAlt_Has_<Feature> tag on a part
  * May also have feature specific tags for various values
  * Some features will have level tags (e.g. hard drive capacity, science experiment quality) that modify some applied values either linearly or exponentially
  * Some tags added automatically based on part modules/crew etc.
  * Opportunity for mod specific patches to remove auto tags before templating
  * A default template for the feature will be added to each part with that tag
  * Opportunity for mod specific patches to override feature specific tag values
  * Feature specific tag values will be used to calculate modifications to template
  * Opportunity to override calculated values with hand-crafted custom values


Example:

// Default values
KERBALISM_ALTERNATE_DEFAULTS
{
  // KAlt_Has_Drill
  KAlt_Drill_Transform = ImpactTransform
  KAlt_Drill_Length = 1
  KAlt_Drill_rate = 1
  KAlt_Drill_EC = 1

  // KAlt_Transmitter
  KAlt_Transmitter_Range = 1
}

// rescales or difficulty modifying mods might override defaults
@KERBALISM_ALTERNATE_DEFAULTS:NEEDS[JNSQ]
{
  @KAlt_Transmitter_Range *= 4
}

// Template declaration
@KERBALISM_ALTERNATE_TEMPLATES:BEFORE[0_KerbalismAlternate]
{
  ISRU
  {
    DRILL
    {
      // various default modules for surface harvester drill
      // uses default values from tags
    }
  }
}

// part specific configuration in patches
@PART[MiniDrill]:NEEDS[KerbalismAlternate]
{
  KAlt_Has_Drill = True
  KAlt_Drill_Length = 1.08
}

// Generic processing

// apply template
@PART[*]:HAS[#KAlt_Has_Drill[?rue]]:BEFORE[zProfileAlternate]
{
  #@KERBALISM_ALTERNATE/ISRU/DRILL {}
}

// update based on tags
@PART[*]:HAS[#KAlt_Has_Drill[?rue]]:FOR[zProfileAlternate]
{
  @MODULE[Harvester],*
  {
    @drill = #$/KAlt_Drill_Transform$
    @length *= #$/KAlt_Drill_Length$
    ...
  }
}

// custom manual values can be applied there
@PART[MiniDrill]:NEEDS[KerbalismAlternate]:AFTER[zzzProfileAlternate]
{
  @MODULE[Harvester]:HAS[<SomeValue>]
  {
    @rate = 1.6 // some special override to calculated values
  }
}
