#Using the GeLoSDK
Add the following permissions to your AndroidManifest.xml

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />

##Notifications
The GeLoSDK uses the LocalBroadcastManager to alert you to updates and events through a broadcasted Intent, which may contain additional bundled information.

- **GELO_BEACON_FOUND** - Sent when a beacon has been discovered. The serializable extra package of the intent will contain an instance of the beacon under the _beacon_ key.

- **GELO_BEACON_EXPIRED** - Sent when a beacons' "time to live" has expired. The serializable extra package of the intent will contain an instance of the beacon under the _beacon_ key.

- **GELO_NEAREST_BEACON_CHANGED** - Sent when a beacon has been discovered that has a stronger signal strength then any of the other known beacons.  The serializable extra package of the intent will contain an instance of the beacon under the _beacon_ key.

- **GELO_NEAREST_BEACON_EXPIRED** - Sent when the nearest beacons' "time to live" has expired. The serializable extra package of the intent will contain an instance of the beacon under the _beacon_ key.

- **GELO_TOUR_LOADED** - Sent when the requested tour has been loaded from the remote source. The serializable extra package of the intent will contain an instance of the tour under the _tour_ key.

- **GELO_TOURS_LOADED** - Sent when the requested array of known tours has been loaded from the remote source. The serializable extra package of the intent will contain an ArrayList of tours under the _tours_ key.

- **GELO_SITE_LOADED** - Sent when the requested site has been loaded from the remote source.  The serializable extra package of the intent will contain an instance of the site under the _site_ key.

- **GELO_SITES_LOADED** - Sent when the requested array of known sites has been loaded from the remote source.  The serializable extra package of the intent will contain an ArrayList of sites under the _sites_ key.

- **GELO_MEDIA_FINISHED** - Sent when all of the queued up media from requested sites and tours has finished loading. Requires caching to be turned on.

- **GELO_SITE_NEEDS_UPDATE** - Sent when the requested site is either not currently cached or is out of date. The extra package of the intent will contain an integer with the site id of the out of date site under the _siteId_ key.

- **GELO_TOUR_NEEDS_UPDATE** - Sent when the requested tour is either not currently cached or is out of date. The extra package of the intent will contain an integer with the tour id of the out of date site under the _tourId_ key.

- **GELO_SITE_UP_TO_DATE** - Sent when the requested site is cached and the cached version of the site is up to date. The serializable extra package of the intent will contain an instance of the site under the _site_ key.

- **GELO_TOUR_UP_TO_DATE** - Sent when the requested tour is cached and the cached version of the tour is up to date. The serializable extra package of the intent will contain an instance of the tour under the _tour_ key.

##Classes

###GeLoBeaconManager

The GeLoBeaconManager is a singleton and should be accessed through its **sharedIstance** method. The beacon manager requires a copy of the application context for access to the Bluetooth service.

#####- public static GeLoBeaconManager sharedInstance(Context context)

		GeLoBeaconManager.sharedInstance(getApplicationContext());


#####- public void startScanningForBeacons()
Used to start the beacon manager scanning for GeLo Beacons in the area.  Beacons that are discovered are reported through the **kGeLoBeaconFound** intent.

	mGeLoBeaconManager.startScanningForBeacon();


#####- public void stopScanningForBeacons()
Used to stop the beacon manager from scanning for more GeLo Beacons.

	mGeLoBeaconManager.stopScanningForBeacon();


#####- public boolean isScanning()
Returns a boolean to indicate whether or not the beacon manager is actively scanning for more beacons.

	boolean value = mGeLoBeaconManager.isScanning();


#####- public ArrayList<GeLoBeacon> getKnownBeacons()
Returns an array of all the beacons that are in the current area that the beacon manager knows about.

	ArrayList<GeLoBeacon> beacons = mGeLoBeaconManager.getKnownBeacons();


#####- public GeLoBeacon getNearestBeacon()
Returns the instance of the beacon that is closest to the device.

	GeLoBeacon nearest = mGeLoBeaconManager.getNearestBeacon();


#####- public void setFalloff(int beaconId, int falloff)
Sets the minimum RSSI for the given beacon id that will allow the beacon manager to trigger a nearest beacon notification.

	mGeLoBeaconManager.setFalloff(98, -76); //moderately close before beacon manager will recognize the beacon


#####- public void setFalloff(int falloff)
Sets the default minimum RSSI for all beacons that will allow the beacon manager to trigger a nearest beacon notification.

	mGeLoBeaconManager.setFalloff(-60); //very close before the beacon manager will recognize the beacon


#####- public void setTimeToLive(int timeToLive)
Sets the default time to live for a beacon. The default value is 15, which means the beacon will last for 15 seconds before it expires.

	mGeLoBeaconManager.setTimeToLive(10);


#####- public void setSignalCeiling(int signalCeiling)
Sets the default maximum RSSI for all beacons. The beacon manager will ignore beacon signals stronger than the given value.

	mGeLoBeaconManager.setSignalCeiling(-40); //any signal stronger than -40 RSSI is ignored.


###- GeLoPlatformManager

The GeLoPlatformManager is a singleton and should be accessed through its **sharedInstance** class method.

		GeLoPlatformManager.sharedInstance(getApplicationContext());


#####- public void setOAuth2(String clientId, String username, String password)
**REQUIRED** Sets OAuth2 authentication credentials for platform access. Credentials can be retrieved from the Gelo Platform.


#####- public ArrayList loadKnownSites()
Returns an array of all the sites currently cached on the device. Sites that are loaded from the platform will be broadcasted through the **GELO_SITES_LOADED** intent.

	ArrayList<GeLoSite> sites = mPlatformManager.loadKnownSites();


#####- public ArrayList loadKnownTours()
Returns an array of all the tours currently cached on the device. Tours that are loaded from the platform will be broadcasted through the **GELO_TOURS_LOADED** intent.

	ArrayList<GeLoTour> tours = mPlatformManager.loadKnownTours();


#####- public GeLoSite loadSiteById(final int siteId)
Returns an instance of the given site cached on the device. If the site is loaded from the platform it will be broadcasted through the **GELO_SITE_LOADED** intent.

	GeLoSite site == mPlatformManager.loadSiteById(90);


#####- public GeLoTour loadTourById(final int tourId)
Returns an instance of the given tour cached on the device. If the tour is loaded from the platform it will be broadcasted through the **GELO_TOUR_LOADED** intent.

	GeLoTour tour == mPlatformManager.loadTourById(45);


#####- public void setCurrentSite(GeLoSite site)
Sets the current site tracked by the platform manager.

	mPlatformManager.setCurrentSite(site);


#####- public void setCurrentTour(GeLoTour tour)
Sets the current tour tracked by the platform manager.

	mPlatformManager.setCurrentTour(tour);


#####- public void shouldCache(boolean shouldCache)
Sets whether the platform manager should cache content to disk. Set to true by default.

	mPlatformManager.shouldCache(true);

###GeLoSite
Instance variables:

- siteId
- backgroundColor
- name
- logoImage
- backgroundImage
- description
- updatedAt
- sections
- tours

###GeLoTour
Instance variables:

- tourId
- siteId
- barColor
- textColor
- sectionTextColor
- name
- updatedAt
- image
- beaconInfos

###GeLoBeacon
Instance variables:

- beaconId
- signalStrength
- recievedRSSI
- txPower
- timeToLive
- version

###GeLoBeaconInfo
Instance variables:

- remoteId
- beaconId
- name
- type
- category
- signalFalloff
