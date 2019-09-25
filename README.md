# daneel
A Machine Learning library to develop agent prototypes in unity fast.

## How to use
Make your playable GameObject extend from NetInterface and implement the "Think" function where you should program your model's inputs and outputs.

You must have something like that:
``` C#
public class PlayableObject : NetInterface
{
    // Object controller
  
    protected override IEnumerator Think()
    {
        float[] inputs;
        while (game_running)
        {
            inputs = new float[] {
                your,
                inputs
            };
            outputs = mind.Compute(inputs);
            
            // Do something with your outputs
           
            yield return new WaitForSeconds(think_frequency);
        }
    }
}
```
After that, write your learning algorithm in your GameController's script. For example:
``` C#
private Genetic genetic;
private GameObject[] samples;
    
private void Start()
{
    genetic.GenerateRandomGeneration();
    samples = genetic.Samples;
    StartCoroutine(Learn());
}    

private IEnumerator Learn()
{
    while (true)
    {
        for (int i = 0; i < samples.Length; i++)
        {
            samples[i].SetActive(true);
            game_running = true;
            first_touch = true;
            while (game_running)
            {
                yield return new WaitForEndOfFrame();
            }
            samples[i].SetActive(false);
            if (samples[i].GetComponent<NetInterface>().Score < new_score)
                samples[i].GetComponent<NetInterface>().Score = new_score;
            ResetGame();
            Debug.Log("Score of sample " + samples[i].GetComponent<NetInterface>().Id + ": " + samples[i].GetComponent<NetInterface>().Score + "\nNext sample incoming...");
        }
        genetic.NextGeneration();
        genetic.SaveBestSample("sample_" + genetic.Best_sample.GetComponent<NetInterface>().Id + ".txt");
        Debug.Log("Best sample: " + genetic.Best_sample.GetComponent<NetInterface>().Id + "\nNext generation in coming.");
        samples = genetic.Samples;
    }
}
```
