## ClassDef KaggleBasePropSetting
**KaggleBasePropSetting**: This class extends `BasePropSetting` and configures properties specific to Kaggle competitions, including paths, classes, and flags for various components involved in data mining and model development.

attributes:
· model_config: Configuration dictionary with environment prefix "KG_" and no protected namespaces.
· scen: Scenario class for the data mining model, set to "rdagent.scenarios.kaggle.experiment.scenario.KGScenario".
· hypothesis_gen: Hypothesis generation class, set to "rdagent.scenarios.kaggle.proposal.proposal.KGHypothesisGen".
· hypothesis2experiment: Class responsible for converting hypotheses into experiments, set to "rdagent.scenarios.kaggle.proposal.proposal.KGHypothesis2Experiment".
· feature_coder: Feature coding class, set to "rdagent.scenarios.kaggle.developer.coder.KGFactorCoSTEER".
· model_feature_selection_coder: Class for selecting features in the model, set to "rdagent.scenarios.kaggle.developer.coder.KGModelFeatureSelectionCoder".
· model_coder: Model coding class, set to "rdagent.scenarios.kaggle.developer.coder.KGModelCoSTEER".
· feature_runner: Feature running class, set to "rdagent.scenarios.kaggle.developer.runner.KGFactorRunner".
· model_runner: Model running class, set to "rdagent.scenarios.kaggle.developer.runner.KGModelRunner".
· summarizer: Class for summarizing hypothesis and experiment feedback, set to "rdagent.scenarios.kaggle.developer.feedback.KGHypothesisExperiment2Feedback".
· evolving_n: Number of evolutions set to 10.
· competition: Name of the Kaggle competition, initialized as an empty string.
· template_path: Base path for Kaggle competition templates, set to "rdagent/scenarios/kaggle/experiment/templates".
· local_data_path: Path to the folder storing Kaggle competition data, initialized as an empty string.
· if_action_choosing_based_on_UCB: Boolean flag to enable decision-making based on the UCB algorithm, set to False.
· domain_knowledge_path: Path to the folder containing domain knowledge files in .case format, set to "/data/userdata/share/kaggle/domain_knowledge".
· rag_path: Base version of vector-based RAG (Retrieval-Augmented Generation), set to "git_ignore_folder/kaggle_vector_base.pkl".
· if_using_vector_rag: Boolean flag to enable basic vector-based RAG, set to False.
· if_using_graph_rag: Boolean flag to enable advanced graph-based RAG, set to False.
· knowledge_base: Knowledge base class, dynamically set to 'KGKnowledgeGraph' when advanced graph-based RAG is enabled; otherwise, it remains an empty string.
· knowledge_base_path: Path to the advanced version of graph-based RAG, set to "kg_graph.pkl".
· auto_submit: Boolean flag to automatically upload and submit each experiment result to the Kaggle platform, set to False.
· mini_case: Boolean flag to enable a mini-case study for experiments, set to False.
· if_using_mle_data: Boolean flag indicating whether MLE (Maximum Likelihood Estimation) data is used, initialized as False.

Code Description: The `KaggleBasePropSetting` class inherits from `BasePropSetting` and provides specific configurations tailored for Kaggle competitions. It includes paths to various resources such as templates, local data, domain knowledge, and RAG models. Additionally, it specifies classes responsible for different stages of the data mining and model development process, including scenario definition, hypothesis generation, feature coding, model selection, running experiments, and summarizing results. The class also contains boolean flags that control the use of advanced features like UCB-based decision-making, vector-based RAG, graph-based RAG, automatic submission to Kaggle, mini-case studies, and MLE data usage.

Note: Usage points include setting up a new Kaggle competition by configuring paths and classes appropriately. Developers can enable or disable specific functionalities using the boolean flags provided in this class. For instance, enabling `if_using_graph_rag` will set the `knowledge_base` attribute to 'KGKnowledgeGraph', integrating advanced graph-based RAG into the workflow. Similarly, setting `auto_submit` to True allows for automatic submission of experiment results to Kaggle, streamlining the process of participating in competitions.
