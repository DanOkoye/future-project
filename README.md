# future-project
Music player(interface)
package BoomBox;

import javafx.application.Application;
import javafx.stage.Stage;
import javafx.scene.Scene;
import javafx.stage.DirectoryChooser;
// this is to import component
import javafx.scene.control.Label;
import javafx.scene.control.ListView;
import javafx.scene.control.Menu;
import javafx.scene.control.MenuBar;
import javafx.scene.control.MenuItem;
import javafx.scene.control.Button;
import javafx.scene.control.TextField;
import javafx.scene.control.Slider;
import javafx.scene.media.Media;
import javafx.scene.media.MediaPlayer;
import javafx.scene.layout.BorderPane;

//this is to import for layout
import javafx.scene.layout.GridPane;
import javafx.geometry.Pos;
import javafx.geometry.Insets;
import java.util.HashMap;
import java.util.Map;
import java.io.File;
import javafx.util.Duration;
import java.text.DecimalFormat;

public class BoomBox extends Application {

	// class components of Label,Button etc.
	Label lblTrack, lblPlayList, lblVolume, lblCurrentTime;
	Button bttnAdd, bttnRemove, bttnRemoveAll, bttnPlay, bttnPause, bttnStop;
	Slider slidMusic, SlidVolume;

	// having textField textTrack, textPlayList;
	ListView<String> ListTrack, ListPlayList;
	Map<String, String> mapArray = new HashMap<>();
	MenuBar mBar;
	MediaPlayer mp3;
	Double musicTime;
	Duration musicDuration;

	public BoomBox() {

	}// constructor

	@Override
	public void init() {

		mBar = new MenuBar();

		// creating all the component buttons as required
		bttnAdd = new Button("Add music");
		bttnRemove = new Button("Remove");
		bttnRemoveAll = new Button("Remove All");
		bttnPlay = new Button("Play");
		bttnPause = new Button("Pause");
		bttnStop = new Button("Stop");

		// Creating components .
		lblPlayList = new Label("Selected Play");
		lblTrack = new Label("Available Track");
		lblVolume = new Label("Volume");
		lblCurrentTime = new Label("");
		slidMusic = new Slider();
		SlidVolume = new Slider(0, 1, 0.5);

		/*
		 * we give all the setMinWidth and Height of 100 and 250 to every components:
		 * buttonRemove, buttonAdd, buttonPlay,buttonPause and slidMusic
		 */
		bttnAdd.setMinWidth(100);
		bttnRemove.setMinWidth(100);
		bttnRemoveAll.setMinWidth(100);
		bttnPlay.setMinWidth(100);
		bttnPause.setMinWidth(100);
		bttnStop.setMinWidth(100);

		// set the slider
		slidMusic.setMinWidth(200);
		SlidVolume.setMaxWidth(100);
		lblVolume.setMinWidth(100);

		// Create listview
		ListTrack = new ListView();
		ListPlayList = new ListView();

		// setting the Listview height
		ListTrack.setMinHeight(250);
		ListPlayList.setMinHeight(300);

		// Handle event on listView available track
		ListTrack.setOnMouseClicked(ae -> {

			
			ListPlayList.getSelectionModel().clearSelection();
			String play = ListTrack.getSelectionModel().getSelectedItem();
			String path = mapArray.get(play);
			path = path.replace("\\", "/");

			// assigning file to media
			Media music = new Media(new File(path).toURI().toString());
			mp3 = new MediaPlayer(music);
			System.out.println(mapArray.get(play));
		});

		// Playlist Listview are on selection mode
		ListPlayList.setOnMouseClicked(ae -> {

			ListTrack.getSelectionModel().clearSelection();
			String boom = ListPlayList.getSelectionModel().getSelectedItem();

			// getting the value of the selected names from the mapArray
			String path = mapArray.get(boom);
			path = path.replace("\\", "/");

			// assigning the file path to the media
			Media music = new Media(new File(path).toURI().toString());

			// assign an object of mediaplayer and give it a media value
			mp3 = new MediaPlayer(music);
			System.out.println(mapArray.get(boom));
		});

		bttnAdd.setOnAction(ae -> {

			//checked if selection is not null!
			if (ListTrack.getSelectionModel().getSelectedItem() != null) {
				String boom = ListTrack.getSelectionModel().getSelectedItem();

				// add from the tracks listview to the playlist
				ListPlayList.getItems().add(boom);
			} else {
				System.out.println("You can Select a track from the Tracks");
			}

		});

		// Event handler to remove button
		bttnRemove.setOnAction(ae -> {

			// checked if an item is selected that will be removed remove from playlist
			if (ListPlayList.getSelectionModel().getSelectedItem() != null) {
				String boom = ListPlayList.getSelectionModel().getSelectedItem();

				// Remove selected item from playlist
				ListPlayList.getItems().remove(boom);
			} else {
				System.out.println("Kindly select a Track from the playlist to Delete");
			}
		});

		//Event handler to remove button
		bttnRemoveAll	.setOnAction(ae -> {

			ListPlayList.getItems().clear();

		});

		// event handler for the play button
		bttnPlay.setOnAction(ae -> {

			// we can play music now
			mp3.play();

			// having a track duration handler
			musicTime = mp3.getTotalDuration().toSeconds();

			//converting total time to minutes
			String totalTimeInMins = (String
					.valueOf(new DecimalFormat("0.00").format(mp3.getTotalDuration().toMinutes())));

			// current time in the music from music player
			mp3.currentTimeProperty().addListener((Observable) -> {

				// slider which update current music.
				if (slidMusic.isPressed()) {
					mp3.seek(Duration.seconds((slidMusic.getValue() * (musicTime) / 100)));
				}

				// setting the value of the slider from in the current music player time
				slidMusic.setValue((mp3.getCurrentTime().toSeconds() * 100) / musicTime);

				//  now making an update of the current time label together with the current time
				lblCurrentTime
						.setText(String.valueOf(new DecimalFormat("#0.00").format(mp3.getCurrentTime().toMinutes()))
								+ " / " + totalTimeInMins);

			});
		});

		//pause button for event handler
		bttnPause.setOnAction(e -> {
			mp3.pause();
		});

		// having a stop button event handler
		bttnStop.setOnAction(e -> {
			mp3.stop();
		});

		//adjusting the volume of the music player from the slider
		SlidVolume.setOnMouseClicked(ae -> {
			mp3.setVolume(SlidVolume.getValue());

		});

		// Creating the File menu and Add MenuItems to file Menu
		Menu thefileMenu = new Menu("File");
		MenuItem open = new MenuItem("Open");
		thefileMenu.getItems().add(open);
		open.setOnAction(ae -> listFiles());

		// Adding menus to MenuBar
		mBar.getMenus().add(thefileMenu);

	}// init

	public void listFiles() {
		//directorychooser to file
		DirectoryChooser Chooser = new DirectoryChooser();
		File thefile = Chooser.showDialog(null);
		File[] myList = thefile.listFiles();
		for (File newfile : myList) {
			if (newfile.isFile()) {

				// populating the track listview from the file list
				ListTrack.getItems().add(newfile.getName());

				/* populating the mapArray with the relative and absolute path*/
				mapArray.put(newfile.getName(), newfile.getAbsolutePath());
				System.out.println(newfile.getAbsolutePath());

			}
		}
	}

	@Override
	public void stop() {

	}

	@Override

	public void start(Stage primaryStage) throws Exception {

		// set the Title
		primaryStage.setTitle("MusicPlayer");

		// set the width and Height
		primaryStage.setWidth(600);
		primaryStage.setHeight(450);

		// create a layout
		GridPane gp = new GridPane();
		BorderPane bp = new BorderPane();
		bp.setCenter(gp);
		bp.setTop(mBar);

		/* set the padding and gaps for the gridpane*/
		gp.setPadding(new Insets(10));
		gp.setHgap(10);
		gp.setVgap(10);

		gp.setAlignment(Pos.CENTER);

		
		// Add Buttons
		gp.add(bttnAdd, 1, 1);
		gp.add(bttnRemove, 1, 2);
		gp.add(bttnRemoveAll, 1, 3);
		gp.add(bttnPlay, 1, 4);
		gp.add(bttnPause, 1, 5);
		gp.add(bttnStop, 1, 6);

		// Add component layout
				gp.add(lblTrack, 0, 0);
				gp.add(lblPlayList, 2, 0);
				gp.add(lblVolume, 1, 7);

		
		// Add the TextField
		gp.add(ListTrack, 0, 1, 1, 9);
		gp.add(ListPlayList, 2, 1, 1, 9);

		
		// create scene
		Scene ss = new Scene(bp);

		/* ss.getStylesheets()*/
		ss.getStylesheets().add(getClass().getResource("BoomBoxstyle1.css").toExternalForm());

				// Adding slider
				gp.add(lblCurrentTime, 3, 10);
				gp.add(slidMusic, 2, 11, 3, 1);
				gp.add(SlidVolume, 1, 8);

		
		// set the scene
		primaryStage.setScene(ss);

		// show stage
		primaryStage.show();

	}// start()

	public static void main(String[] args) {
		// Launch the application
		launch();
	}

}
