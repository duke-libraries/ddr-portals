# DDR-Portals

DDR-Portals contains configurations and template overrides for Digital Collections in the Duke's Digital Repository.

## Syncing with DDR-Public

DDR-Public has rake tasks to sync with the latest version of DDR-Portals. To update DDR-Public with the master branch of DDR-Portals run the following rake command in DDR-Public:

```sh
$ rake ddr_public:ddr_portals:sync
```

You can specify a branch of DDR-Portals by adding an environmental variable to the command:

```sh
$ rake ddr_public:ddr_portals:sync BRANCH=develop
```

## Configuring a Portal

A portal configuration comprises a directory named for the portal that contains yml files that set configurations for the portal as well as a directory structure for view partial overrides.

The directory structure generally looks like follwing. (The views directory may be excluded if view partial overrides are not needed.)

```
ddr-portals/
    wdukesons/
        views/
            # This directory must be named for the controller of the portal in order for Rails to find it.
            digital_collections/
                _show_license_default.html.erb
        portal_doc_configs.yml
        portal_view_configs.yml
```        

### portal_doc_configs.yml

This configuration file contains settings that pertain to the collection and defines what controller should own the collection, the id that should be used for items in the collection (local_id or id) and the thumbnail image that should be used for the collection document. This file is required but may not contain configurations if the portal does not coorespond with a collection document, such as in the case of the Digital Collections portal.

```yml
defaults: &defaults
    # Specifies the identifier field
    collection_local_id:
        # Specifies the identifier for the collection
        wdukesons:
            # The controller that owns this collection
            controller: digital_collections
            # How should the collection be identified in the URL
            collection: wdukesons
            # What ID should be used in the URL for items in this collection
            item_id_field: local_id
            # What item should be used to provide the thumbnail for the collection
            thumbnail_image: dscsi03005

development:
  <<: *defaults

test:
  <<: *defaults

production:
  <<: *defaults
```

### portal_view_configs.yml

This file contains settings that define what items belong to the portal, the images that should be displayed in the showcase and highlight sections of the portal landing page (the index view of the controller), blog post URL, and any alerts that should be displayed. It also defines the blacklight configurations that should be applied the controller when displaying the portal page for this collection.

```yml
defaults: &defaults
    # The items (defined by the id of the parent collections) should be included in this portal
    includes:
        local_ids:
           - wdukesons
    # The (if any) showcase images should be displayed prominently in the portal index view. (Defined by the local_ids of the items.)
    showcase_images:
      local_ids:
         - dscsi02223
         - dscsi02230
         - dscsi02371
         - dscsi02373
      layout: portrait
    # The (if any) highlight images should be displayed in the portal index view. (Defined by the local_ids of the items.)
    highlight_images:
      local_ids:
        - dscsi02317
        - dscsi02331
        - dscsi04001
        - dscsi04016
    blog_posts: https://blogs.library.duke.edu/bitstreams/api/get_tag_posts/?tag_slug=wdukesons
    alert: 'Welcome to the W. Duke & Sons collection...</a>'
    # The blacklight configurations that should be applied to the controller for this collection.
    configure_blacklight:
        add_facet_field:
          - field: "Ddr::Index::Fields::ACTIVE_FEDORA_MODEL"
            label: Browse
            show: false
          - field: "Ddr::Index::Fields::YEAR_FACET"
            label: Year
            limit: 9999
            collapse: false
            range:
                num_segments: 6
                segments: true
        add_show_field:
          - field:
              - :title
              - :stored_searchable
            separator: "; "
            label: "Title"
          - field: "Ddr::Index::Fields::IS_MEMBER_OF_COLLECTION"
            label: "Collection"
            helper_method: descendant_of
          - field:
            - :duketerms_series
            - :stored_searchable
            separator: "; "
            label: "Card Series"
            link_to_search: "Ddr::Index::Fields::SERIES_FACET"

development:
  <<: *defaults

test:
  <<: *defaults

production:
  <<: *defaults
```


