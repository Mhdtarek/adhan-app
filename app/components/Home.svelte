<script lang="ts">
  import { remove } from "@nativescript/core/application-settings";
  import { Coordinates, CalculationMethod, PrayerTimes } from "adhan";
  import * as geolocation from "@nativescript/geolocation";
  import { CoreTypes } from "@nativescript/core";
  CoreTypes.Accuracy; // used to describe at what accuracy the location should be get

  var lat;
  var lng;
  let coordinates;
  let params;
  let date;
  let prayerTimes;
  let hasFetched = false;
  function removeDate(date) {
    var hours = date.getHours();
    var hr = hours < 10 ? "0" + hours : hours;

    var minutes = date.getMinutes();
    var min = minutes < 10 ? "0" + minutes : minutes;

    return hr + ":" + min;
  }

  if (!geolocation.isEnabled()) {
    geolocation.enableLocationRequest().then(
      function () {
        console.error("Allow geolocation");
      },
      function (error) {
        console.error("Deny : " + error);
      }
    );
  }

  geolocation
    .getCurrentLocation({
      desiredAccuracy: 1,
      updateDistance: 10,
      minimumUpdateTime: 600000,
      maximumAge: 600000,
      timeout: 5000,
    })
    .then(
      function (location) {
        lat = location.latitude;
        lng = location.longitude;

        coordinates = new Coordinates(lat, lng);
        params = CalculationMethod.UmmAlQura();
        date = new Date();
        prayerTimes = new PrayerTimes(coordinates, date, params);
        hasFetched = true;
      },
      function (e) {
        console.log("Error: " + e.message);
      }
    );
</script>

<page>
  {#if hasFetched}
    <actionBar title="Home" />
    <gridLayout columns="*, *" rows="*, *, *, *, *" backgroundColor="#deab90">
      <flexboxLayout
        flexDirection="column"
        backgroundColor="#8a5a44"
        colSpan="2"
        col="0"
        row="0"
      >
        <label text="Fajr" height="*" class="center-1" />
        <label
          text={removeDate(prayerTimes.fajr)}
          height="*"
          class="center-2"
        />
      </flexboxLayout>

      <flexboxLayout
        flexDirection="column"
        backgroundColor="#9d6b53"
        col="0"
        row="1"
      >
        <label text="Sunrise" height="*" class="center-1" />
        <label
          text={removeDate(prayerTimes.sunrise)}
          height="*"
          class="center-2"
        />
      </flexboxLayout>

      <flexboxLayout
        flexDirection="column"
        backgroundColor="#b07d62"
        col="1"
        row="1"
      >
        <label text="Dhuhr" height="*" class="center-1" />
        <label
          text={removeDate(prayerTimes.dhuhr)}
          height="*"
          class="center-2"
        />
      </flexboxLayout>

      <flexboxLayout
        flexDirection="column"
        backgroundColor="#c38e70"
        col="0"
        row="2"
      >
        <label text="Asr" height="*" class="center-1" />
        <label text={removeDate(prayerTimes.asr)} height="*" class="center-2" />
      </flexboxLayout>

      <flexboxLayout
        flexDirection="column"
        backgroundColor="#cd9777"
        col="1"
        row="2"
      >
        <label text="Maghrib" height="*" class="center-1" />
        <label
          text={removeDate(prayerTimes.maghrib)}
          height="*"
          class="center-2"
        />
      </flexboxLayout>

      <flexboxLayout
        flexDirection="column"
        backgroundColor="#d69f7e"
        col="0"
        row="3"
        colSpan="2"
      >
        <label text="Isha" height="*" class="center-1" />
        <label
          text={removeDate(prayerTimes.isha)}
          height="*"
          class="center-2"
        />
      </flexboxLayout>
    </gridLayout>
  {/if}
</page>

<style>
  .center-1 {
    padding-top: 51px;
    text-align: center;
    font-size: 25;
  }
  .center-2 {
    padding: 95px 0;
    text-align: center;
    font-size: 15;
  }
  actionBar {
    height: 0;
  }
</style>
