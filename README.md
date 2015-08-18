# prueba

El programa abre un mapa con las localizaciones del JSON la posicion del telefono y el centroid de estas posiciones

El codigo esta en el archivo comprimido centroid.rar el cual se debe descomprimir en ese mismo punto para abrir todo en un workspace y editarlo en eclipse.

Se crearon dos clases

centroid.java que calcula el centroide

public class centroid { static public double[] polygonCentroid (double[] points) { double x = 0, y = 0; int count = points.length; for (int i = 0; i < count; i++) { x += points[i]; i++; y += points[i]; } double[] center = new double[2]; center[0] = 2x/count;
 center[1] = 2y/count; return center; } }

Esta clase recibe un arreglo con las posiciones del JSON y la posicion de mi telefono y retorna el centroide

MainActivity.java que consume el JSON, lee la posicion de un telefono y con esas localizaciones se invoca un metodo de la clase centroid que hace el calculo y despliega en un mensaje el resultado de latitud y longitud del centroide

Se utiliza el array "arrayConsuming" para guardar las posiciones.

La ejecucion del proceso se realiza cuando se abre el programa y tambien con el boton "Centroide"
Al oprimir el boton "btnGet" consume el JSON y guarda en el arreglo "myTelephone" mi ubicacion obtenida previamente con "location" public void btnGet(View view) { new ReadJSONDriver().execute( "https://raw.githubusercontent.com/tappsi/test_recruiting/master/sample_files/driver_info.json");
 double[] myTelephone = new double[2]; myTelephone = myPos();
      arrayConsuming[6] = myTelephone[0];
      arrayConsuming[7] = myTelephone[1];
}

La localizacion del telefono se intenta primero por GPS y si falla se hace por NETWORK

Location location = locationManager.getLastKnownLocation(LocationManager.GPS_PROVIDER);

  if (location != null) {
       vlat = location.getLatitude();
       vlon = location.getLongitude();
  }
  else
  {
    location = locationManager.getLastKnownLocation(LocationManager.NETWORK_PROVIDER);
    vlat = location.getLatitude();
    vlon = location.getLongitude();
  }
  final double[] arraymyPos = new double[2];
  arraymyPos[0] = vlat;
  arraymyPos[1] = vlon;
  return arraymyPos;


En onPostExecute se lee el JSON y se carga en un arreglo que previamente tambien se ha cargado con la posicion del telefono, despues se invoca el metodo polygonCentroid pasandole este arreglo para el calculo del centroide
    protected void onPostExecute(String result) {
        try {
          JSONObject json = new JSONObject(result);
          JSONArray driverInfo = json.getJSONArray("bookings");
          int i1 = 0;
          for (int i = 0; i < driverInfo.length();  i++ ) 
          {   
            JSONObject c = driverInfo.getJSONObject(i);
            String vlat = c.getString("lat");
            String vlon = c.getString("lon");
            arrayConsuming[i1] = Double.parseDouble(vlat);
            i1 += 1;
            arrayConsuming[i1] = Double.parseDouble(vlon);
            i1 += 1;
          }        
          double[] array = centroid.polygonCentroid(arrayConsuming);
          Toast.makeText(getBaseContext(), 
              String.valueOf(array[0]) + " , " + String.valueOf(array[1]), 
             Toast.LENGTH_SHORT).show();


        } catch (Exception e) {
            Log.d("ReadJSONDriver", e.getLocalizedMessage());
        } 
    }
