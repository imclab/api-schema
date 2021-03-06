# Changelog

## v2 Revision 1

### All resources

 * Went from GET endpoints to RESTful resources
 * Modeled long-running operations as "action" sub-resources, dropped events endpoint
 * OAuth used for all authentication (Personal tokens available for dev/debugging)
 * Uses Range headers for managing and configuring pagination

### Size resource

 * Switched to slug as primary and only identifier
 * Added fields memory, cpus, disk, transfer, price_monthly, price_hourly
 * Added available regions
 
### Region resource

 * Switched to slug as primary and only identifier
 * Added available sizes  
 * Added available boolean field

### Key resource
 
 * Renamed SSH keys to just keys
 * Added fingerprint field
 * Made keys immutable, as standard across similar APIs

### Image resource

 * Added regions the image is available in
 * Allow changing the image name
 * Added image size

### Droplet resource

 * Changed region_id to just region as reference to region slug
 * Changed image_id and size_id to expanded embedded objects under image and size
 * Removed backups_active from attributes. Implied by null backups attribute
 * Added networks field with named network objects including netmask and gateway fields
 * Renamed ssh_key_ids to key_ids

### Droplet Action resource

 * Rolled power_cycle into reboot as "hard" reboot. Default is "soft"
 * Rolled power_off into shutdown as "hard" shutdown. Default is "soft"
 * Renamed power_on to boot
 * Action to rename is not available. Edit droplet resource instead.
 * Action status is either "in-progress" or "finished"
