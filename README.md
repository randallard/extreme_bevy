# progress
up to "The rough outline of what we want to do now, is:"
at https://johanhelsing.studio/posts/extreme-bevy

# extreme_bevy
johanhelsing example of matchbox and bevy from https://johanhelsing.studio/posts/extreme-bevy

lots of install steps there, then run with

    cargo run --target wasm32-unknown-unknown

or better

    cargo watch -cx "run --target wasm32-unknown-unknown"

on https://johanhelsing.studio/posts/extreme-bevy-3

Was

        .add_loading_state(
            LoadingState::new(GameState::AssetLoading)
                .with_collection::<ImageAssets>()
                .continue_to_state(GameState::Matchmaking), // <-- CHANGED
        )

should be
      
        .add_loading_state(
            LoadingState::new(GameState::AssetLoading)
                .load_collection::<ImageAssets>()
                .continue_to_state(GameState::Matchmaking), // <-- CHANGED
        )

was

        .add_systems(
            OnEnter(GameState::Matchmaking),
            (setup, start_matchbox_socket),
        )
        .add_systems(OnEnter(GameState::InGame), spawn_players)
        .add_systems(
            Update,
            (
                wait_for_players.run_if(in_state(GameState::Matchmaking)),
                camera_follow.run_if(in_state(GameState::InGame)),
            ),
        )
        .add_systems(ReadInputs, read_local_inputs)
        .run();

should include .add_systems(GgrsSchedule, move_players) 

        .add_systems(
            OnEnter(GameState::Matchmaking),
            (setup, start_matchbox_socket),
        )
        .add_systems(OnEnter(GameState::InGame), spawn_players)
        .add_systems(
            Update,
            (
                wait_for_players.run_if(in_state(GameState::Matchmaking)),
                camera_follow.run_if(in_state(GameState::InGame)),
            ),
        )
        .add_systems(ReadInputs, read_local_inputs) 
        .add_systems(GgrsSchedule, move_players) 
        .run();

was

        fn wait_for_players(
            mut commands: Commands,
            mut socket: ResMut<Option<WebRtcSocket>>,
            mut next_state: ResMut<NextState<GameState>>, // <-- NEW
        ) {
            // ...

            next_state.set(GameState::InGame); // <-- NEW
        }

should be ResMut<MatchboxSocket<SingleChannel>>

        fn wait_for_players(
            mut commands: Commands,
            mut socket: ResMut<MatchboxSocket<SingleChannel>>,
            mut next_state: ResMut<NextState<GameState>>,
        ) {
            //  ...

            next_state.set(GameState::InGame); // <-- NEW
        }



