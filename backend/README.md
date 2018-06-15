Requires PHP 7.1
On ubuntu :
https://launchpad.net/~ondrej/+archive/ubuntu/php

Requires : php-xml

Then run :
./composer-install.php
./composer-run.php

settings asked by installation :
database_host (db): 127.0.0.1
database_port (null): 3306
database_name (openloyalty): 
database_user (openloyalty): 
database_password (openloyalty): 
database_driver (pdo_pgsql): 
database_version (9): 
elastica ({ hosts: ['elk:9200'] }): 
secret (ThisTokenIsNotSoSecretChangeIt): 
jwt_private_key_path ('%kernel.root_dir%/var/jwt/private.pem'): 
jwt_public_key_path ('%kernel.root_dir%/var/jwt/public.pem'): 
jwt_key_pass_phrase (''): 
jwt_token_ttl (86400): 
pagination_per_page (10): 
mailer_transport (smtp): 
mailer_host (mail): 
mailer_user (null): 
mailer_password (null): 
mailer_port (1025): 
mailer_encryption (null): 
mailer_from_address (open@oloy.com): 
mailer_from_name (open@oloy.com): 
frontend_password_reset_url ('%env(frontend_password_reset_url)%'): 
frontend_activate_account_url ('%env(frontend_activate_account_url)%'): 
frontend_customer_panel_url ('%env(frontend_customer_panel_url)%'): 
env(frontend_password_reset_url) ('localhost:8182/#!/password/reset'): 
env(frontend_activate_account_url) ('#!/customer/panel/customer/registration/activate'): 
env(frontend_customer_panel_url) ('http://localhost:8183/'): 
loyalty_program_name (OpenLoyalty): 
ecommerce_address ('http://ecommerce.local'): 
customer_search_max_results (3): 
es_max_result_window_size (10000): 
fallback_frontend_translations_file (english.json): 
campaign_photos_adapter (campaign_photos_local): 
campaign_photos_adapter_env (CAMPAIGN_PHOTOS_ADAPTER): 
level_photos_adapter (level_photos_local): 
level_photos_adapter_env (LEVEL_PHOTOS_ADAPTER): 
earning_rule_photos_adapter (earning_rule_photos_local): 
earning_rule_photos_adapter_env (EARNING_RULE_PHOTOS_ADAPTER): 


# api login
## jwt token structure
```
{
 exp: 1470299383,
 username: "admin",
 roles: [
  "ROLE_ADMIN"
 ],
 iat: "1470212983"
}
```
## obtaining jwt token
admin url: /api/admin/login_check
customer url: /api/customer/login_check
seller url: /api/seller/login_check

method: POST

body:
- _username: admin
- _password: open
    
response:
```
{
"token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXUyJ9.eyJleHA",
"refresh_token":"44588da231e2f480bd4"
}
```
## refreshing jwt token
url: /api/token/refresh

method: POST

body:
- refresh_token: 'token'

response:
```
{
"token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXUyJ9.eyJleHA",
"refresh_token":"44588da231e2f480bd4"
}
```

# events

## system events
Proper listener should be created to handle system events.
Example definition of such listener:
```
    oloy.listener:
        class: 'OpenLoyalty\Listener.php'
        tags:
          - { name: broadway.event_listener, event: oloy.customer.registered, method: handle }
```
### 'oloy.customer.registered' - customer added to PL
Event class: OpenLoyalty\Domain\Customer\SystemEvent\CustomerRegisteredSystemEvent

### 'oloy.customer.updated'
Event class: OpenLoyalty\Domain\Customer\SystemEvent\CustomerUpdatedSystemEvent

### 'oloy.customer.agreements_updated'
Event class: OpenLoyalty\Domain\Customer\SystemEvent\CustomerAgreementsUpdatedSystemEvent

### 'oloy.customer.deactivated'
Event class: OpenLoyalty\Domain\Customer\SystemEvent\CustomerDeactivatedSystemEvent

### 'oloy.segment.customer_added_to_segment'
Event class: OpenLoyalty\Domain\Segment\SystemEvent\CustomerAddedToSegmentSystemEvent

### 'oloy.segment.customer_removed_from_segment'
Event class: OpenLoyalty\Domain\Segment\SystemEvent\CustomerRemovedFromSegmentSystemEvent

## domain events
Proper listener should be created to handle domain events.
This listener must implement Broadway\EventHandling\EventListenerInterface

Example definition of such listener:
```
    oloy.listener:
        class: 'OpenLoyalty\Listener.php'
        lazy: true
        tags:
          - { name: broadway.domain.event_listener }
```

### OpenLoyalty\Domain\Customer\Event\CampaignWasBoughtByCustomer - dispatched when campaign was bought by customer
