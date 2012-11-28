# itmsp
** CLI for generating app store assets for Game Center and In-App Purchases **

Apple recently released their Transporter app (http://bit.ly/UcEhAh) which is a handy way to manage your App Store Packages (.itmsp) instead of having to input achievements, leaderboards, and in-app purchases through iTunes Connect. Unfortunately, there isn't an easy way to generate these packages. Why write XML by hand and deal with the hassle of calculating MD5 and file sizes? Why not define your Game Center and In-App Purchases in a simple format and have your App Store Package generated for you?

## Installation
```sh
$ gem install itmsp
```

## Usage

### Get Familiar With The Spec
First, you'll need to be familiar with the app metadata specification (download from [here](http://bit.ly/TtHMF6)), which outlines all the possible information describing an achievement, leaderboard, or in-app purchase.

### Describe your application metadata
Next, you'll need to create a YAML file describing your company information and achievement, leaderboard, and/or in-app purchase details.

### Generate the .itmsp
This is as simple as running the following command in the folder containing your YAML config file and all referenced images.

```sh
$ itmsp package -i <configuration file>
```

### That's It!
You can now upload your .itmsp package to iTunes Connect using the Transporter tool. Alternatively, there is another great open-source tool which wraps some of the complexities of running Transporter directly (https://github.com/sshaw/itunes_store_transporter)

## Sample Config file
Below is a sample configuration file describing an achievement, leaderboard, and in-app purchase. The specific metadata required for each item type is fully described in the [App Metadata Specification guide](http://bit.ly/TtHMF6)

```yaml
provider: SampleCompany # optional if team_id is supplied
team_id: ABCDE12345 # optional if provider is supplied
vendor_id: sample sku # the application's SKU as definied in iTunes Connect 
id_prefix: com.samplecompany.applicationname. # if supplied, this will be prefixed to achievement, leaderboard, and in-app purchase IDs
achievements: # the order in which achievements are defined will be the order in which they appear in Game Center
- id: first_achievement
  name: First Achievement
  points: 10
  locales:
      - name: en
        title: First Achievement
        before_earned_description: 'Complete a task'
        after_earned_description: 'Completed a task'
        after_earned_image: achievement.jpg
        
leaderboards: # the order in which leaderboards are defined will be the order in which they appear in Game Center
- default: true # only one leaderboard can be set as default. 
  id: top_scores
  name: Top Scores
  locales:
      - name: en
        title: Top Scores
        formatter_suffix: ' Points' # note the space. This must be provided if you want a space between the value and suffix
        formatter_suffix_singular: ' Point'
        formatter_type: INTEGER_COMMA_SEPARATOR
        leaderboard_image: leaderboard.jpg
        
purchases:
- product_id: one_hundred_dollars
  reference_name: '100 dollars'
  type: consumable
  review_screenshot_image: product1.jpg
  products:
      - cleared_for_sale: true
        wholesale_price_tier: 1 # pricing tier matrix is available in Exhibit C of the iOS Paid Applications contract in the Contracts, Tax, and Banking section of iTunes Connect 
  locales:
      - name: en
        title: '$100'
        description: 'An extra $100 to help you out'
```

This configuration will generate the following metadata.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<package xmlns="http://apple.com/itunes/importer" version="software5.0">
  <provider>SampleCompany</provider>
  <team_id>ABCDE12345</team_id>
  <software>
    <vendor_id>sample sku</vendor_id>
    <software_metadata>
      <game_center>
        <achievements>
          <achievement position="1">
            <achievement_id>com.samplecompany.applicationname.first_achievement</achievement_id>
            <reference_name>First Achievement</reference_name>
            <points>10</points>
            <hidden>false</hidden>
            <reusable>false</reusable>
            <locales>
              <locale name="en">
                <title>First Achievement</title>
                <before_earned_description>Complete a task</before_earned_description>
                <after_earned_description>Completed a task</after_earned_description>
                <after_earned_image>
                  <file_name>test.jpg</file_name>
                  <size>20092</size>
                  <checksum type="md5">ff5bd97a5f40bb75a84884589ecbfc42</checksum>
                </after_earned_image>
              </locale>
            </locales>
          </achievement>
        </achievements>
        <leaderboards>
          <leaderboard default="true" position="1">
            <leaderboard_id/>
            <reference_name>Top Scores</reference_name>
            <sort_ascending>false</sort_ascending>
            <score_range_min/>
            <score_range_max/>
            <locales>
              <locale name="en">
                <title>Top Scores</title>
                <formatter_suffix> Points</formatter_suffix>
                <formatter_suffix_singular> Point</formatter_suffix_singular>
                <formatter_type>INTEGER_COMMA_SEPARATOR</formatter_type>
                <leaderboard_image>
                  <file_name>test.jpg</file_name>
                  <size>20092</size>
                  <checksum type="md5">ff5bd97a5f40bb75a84884589ecbfc42</checksum>
                </leaderboard_image>
              </locale>
            </locales>
          </leaderboard>
        </leaderboards>
        <in_app_purchases>
          <in_app_purchase>
            <product_id>com.samplecompany.applicationname.one_hundred_dollars</product_id>
            <reference_name>100 dollars</reference_name>
            <type>consumable</type>
            <products>
              <product>
                <cleared_for_sale>true</cleared_for_sale>
                <wholesale_price_tier>1</wholesale_price_tier>
              </product>
            </products>
            <locales>
              <locale name="en">
                <title>$100</title>
                <description>An extra $100 to help you out</description>
              </locale>
            </locales>
            <review_screenshot>
              <file_name>test.jpg</file_name>
              <size>20092</size>
              <checksum type="md5">ff5bd97a5f40bb75a84884589ecbfc42</checksum>
            </review_screenshot>
          </in_app_purchase>
        </in_app_purchases>
      </game_center>
    </software_metadata>
  </software>
</package>
```
## Contact

Colin Humber

- http://github.com/colinhumber
- http://twitter.com/colinhumber

## License

itmsp is available under the MIT license. See the LICENSE file for more info.